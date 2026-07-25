# Gegenmaßnahmen: Suricata IDS & Firewall

**Projekt:** WiFi Pineapple – Rogue AP & ARP-Spoofing (Projektaufgabe Nr. 2)
**Verantwortlich:** Person C
**Bezug:** Abschnitt 5 des Abschlussberichts (Gegenmaßnahmen)

Diese Dokumentation beschreibt, wie das Intrusion Detection System Suricata und eine iptables-Firewall im Testnetz eingesetzt werden, um die beiden Angriffsvektoren (Rogue Access Point und ARP-Spoofing) zu erkennen und unautorisierte Geräte präventiv zu blockieren. In Absprache mit der Dozentin (Mevre Tunca, 07.07.2026) werden die Suricata-Maßnahmen theoretisch dargestellt, das heißt ohne separaten Live-Mitschnitt. Die beschriebenen Schritte sind so gehalten, dass sie auf der dokumentierten Ubuntu-Suricata-VM vollständig reproduzierbar sind.

Alle IP-Adressen, VM-Bezeichnungen und Netzwerknamen entsprechen dem verbindlichen IP-Plan und dem VM-Setup aus der Netz-Dokumentation. Die Suricata-VM ist die Ubuntu-Server-VM `Ubuntu-Suricata` mit der Adresse 192.168.30.2 im internen VirtualBox-Netz `labnet`, ihr Netzwerkadapter heißt `enp0s3`.

---

## 1. Rolle von Suricata im Testnetz

Suricata läuft auf einer eigenen Ubuntu-Server-VM und beobachtet den Verkehr im `labnet`. Das IDS greift selbst nicht in die Kommunikation ein, sondern vergleicht die vorbeilaufenden Pakete mit einem Satz von Regeln und schreibt bei einem Treffer einen Alert. Dadurch wird sichtbar, dass ein Angriff stattfindet: Beim Rogue AP läuft der Verkehr des Opfers unverschlüsselt über den Angreifer, beim ARP-Spoofing wird der Verkehr des Opfers über die Kali-VM umgeleitet. Beides erzeugt Muster, die Suricata bzw. die ergänzende Erkennung feststellen kann.

Wichtig für das Verständnis: Suricata arbeitet auf IP- und Anwendungsebene. Es erkennt daher nicht den Rogue Access Point als Funkgerät, sondern dessen Folge, nämlich mitlesbaren Klartext-Verkehr. Für die reine ARP-Ebene (Layer 2) wird Suricata durch arpwatch ergänzt, das genau auf den Wechsel von MAC-Adressen spezialisiert ist.

---

## 2. Installation von Suricata

Die folgenden Schritte werden auf der Ubuntu-Suricata-VM ausgeführt. Für die Installation und das Nachladen der Regeln benötigt die VM kurz Internetzugang. Dazu wird der Netzwerkadapter in VirtualBox vorübergehend auf NAT gestellt und nach Abschluss wieder auf `labnet` zurückgesetzt, wie es auch bei den anderen VMs gehandhabt wurde. Nach dem Zurückstellen muss die statische IP erneut gesetzt werden, weil NAT zwischenzeitlich eine andere Adresse vergibt.

Zunächst die Paketquellen aktualisieren und Suricata aus der offiziellen OISF-Quelle installieren. Diese liefert eine aktuelle Version 8:

```
sudo add-apt-repository ppa:oisf/suricata-stable -y
sudo apt update
sudo apt install suricata -y
```

Anschließend die Version prüfen, um sicherzugehen, dass die Installation sauber ist:

```
suricata --build-info | head -n 3
```

Danach das Regelset der Emerging Threats Open holen. Das sind tausende fertige Signaturen, die Suricata als Basis nutzt:

```
sudo suricata-update
```

Zum Schluss die VM wieder auf `labnet` zurückstellen und die IP 192.168.30.2 erneut setzen. Die dauerhafte IP-Konfiguration liegt bei der Suricata-VM in `/etc/netplan/50-cloud-init.yaml` und lautet:

```
network:
    version: 2
    ethernets:
        enp0s3:
            addresses:
                - 192.168.30.2/24
            routes:
                - to: default
                  via: 192.168.30.1
```

Angewendet wird sie mit:

```
sudo chmod 600 /etc/netplan/50-cloud-init.yaml
sudo netplan apply
```

---

## 3. Eigene Regeln einbinden und Suricata starten

Neben den mitgelieferten ET-Open-Regeln nutzt das Projekt einen eigenen Regelsatz, der gezielt die beiden Angriffsvektoren adressiert. Diese Regeln liegen im Repository unter `skripte/suricata/local.rules`, die zugehörige Konfiguration unter `skripte/suricata/suricata.yaml`. Die inhaltliche Erklärung jeder einzelnen Regel steht in `Regelwerk-Analyse.md`; hier geht es nur um das Einbinden.

Die Regeldatei wird an den Ort kopiert, an dem Suricata seine Regeln erwartet:

```
sudo cp local.rules /var/lib/suricata/rules/local.rules
```

In der Datei `/etc/suricata/suricata.yaml` wird die eigene Regeldatei unter dem Abschnitt `rule-files` ergänzt, sodass sie zusätzlich zu den Standardregeln geladen wird:

```
rule-files:
  - suricata.rules
  - local.rules
```

Vor dem Start prüft man die Konfiguration im Testmodus. Dabei lädt Suricata alle Regeln, ohne aktiv zu lauschen, und meldet eventuelle Syntaxfehler:

```
sudo suricata -T -c /etc/suricata/suricata.yaml -v
```

Läuft der Test ohne Fehler durch, wird Suricata als Dienst gestartet. Dabei lauscht es am Adapter `enp0s3`, also am `labnet`:

```
sudo systemctl restart suricata
sudo systemctl status suricata
```

Der Dienst startet ab jetzt automatisch beim Hochfahren der VM. Damit ist das IDS aktiv und protokolliert Treffer in `/var/log/suricata/fast.log` sowie ausführlicher in `/var/log/suricata/eve.json`.

Zusätzlich wird arpwatch als zweite Erkennungsschicht installiert und aktiviert. Es meldet, wenn sich für eine bekannte IP plötzlich die MAC-Adresse ändert, was das typische Kennzeichen von ARP-Spoofing ist:

```
sudo apt install arpwatch -y
sudo systemctl enable --now arpwatch
```

---

## 4. Was die Regeln erkennen

Der eigene Regelsatz besteht aus sechs Regeln, die beide Angriffsvektoren abdecken. Für den Rogue Access Point greifen drei Regeln auf HTTP-Ebene. Sie erkennen unverschlüsselten HTTP-Verkehr auf die Testseite neverssl.com, Zugangsdaten, die per HTTP-POST im Klartext übertragen werden, und HTTP Basic Authentication, bei der Benutzername und Passwort nur Base64-kodiert und damit praktisch offen übermittelt werden. Alle drei greifen nur bei unverschlüsseltem Verkehr, was die Kernaussage stützt: Über den Rogue AP ist alles mitlesbar, was nicht durch TLS geschützt ist.

Für das ARP-Spoofing sind drei weitere Regeln zuständig. Eine macht ARP-Aktivität im Netz sichtbar, eine erkennt DNS-Anfragen des Opfers, die nach der Umleitung über den Angreifer laufen, und eine schlägt an, wenn eine DNS-Antwort auf die IP der Kali-VM (192.168.30.10) zeigt, was ein direkter Hinweis auf manipulierte Namensauflösung ist. Da reines ARP auf Layer 2 liegt und für ein IP-basiertes IDS nur begrenzt greifbar ist, übernimmt arpwatch hier die verlässliche Erkennung des MAC-Wechsels. Die detaillierte Beschreibung jeder Regel steht in `Regelwerk-Analyse.md`.

---

## 5. Präventive Abwehr durch die Firewall

Während Suricata den Angriff erkennt und meldet, sorgt die iptables-Firewall auf der Ubuntu-Suricata-VM dafür, dass unautorisierte Geräte gar nicht erst am Testnetz teilnehmen. Grundgedanke ist eine Whitelist: Nur die bekannten, legitimen Geräte des `labnet` dürfen kommunizieren, alles andere wird verworfen. Dadurch wird ein untergeschobenes Gerät wie der Rogue Access Point (192.168.30.5) blockiert, sobald es nicht auf der Liste steht.

Umgesetzt wird das über die MAC-Adressen der erlaubten Geräte. Die Standardregel setzt die Policy auf Verwerfen, anschließend werden die bekannten Adressen freigegeben:

```
sudo iptables -P INPUT DROP
sudo iptables -A INPUT -m mac --mac-source <MAC-Gateway> -j ACCEPT
sudo iptables -A INPUT -m mac --mac-source <MAC-Opfergeraet> -j ACCEPT
```

Die konkreten MAC-Adressen werden dabei aus dem tatsächlichen Laboraufbau eingetragen. Ein Gerät, dessen Adresse nicht hinterlegt ist, erhält keine Verbindung, womit ein unautorisierter Access Point oder ein fremder Rechner ausgesperrt wird.

Beim ARP-Spoofing ist eine ehrliche Einordnung wichtig: iptables arbeitet auf IP-Ebene und kann gefälschte ARP-Antworten nicht direkt unterbinden, da diese eine Schicht tiefer liegen. Die passende präventive Maßnahme auf dieser Ebene sind statische ARP-Einträge, bei denen die MAC-Adresse des echten Gateways fest hinterlegt wird, sodass gefälschte Antworten der Kali-VM ignoriert werden:

```
sudo arp -s 192.168.30.1 <MAC-des-echten-Gateways>
```

In einer produktiven Umgebung würde diese Aufgabe zusätzlich von der Dynamic ARP Inspection eines Managed Switch übernommen. Für das Testnetz genügt die Kombination aus MAC-Whitelist in iptables gegen unautorisierte Geräte und statischem ARP-Eintrag gegen die Gateway-Fälschung.

---

## 6. Vorher/Nachher-Betrachtung

Die Wirksamkeit der Gegenmaßnahmen lässt sich als Vorher/Nachher darstellen, was zugleich die Grundlage für die ROSI-Bewertung bildet. Ohne IDS und Firewall laufen beide Angriffe unbemerkt: Der Verkehr des Opfers wird über den Angreifer geleitet und mitgelesen, ohne dass eine Meldung entsteht. Mit aktivem Suricata wird derselbe Angriff sofort als Alert protokolliert, und mit der Firewall-Whitelist wird ein unautorisiertes Gerät bereits an der Teilnahme am Netz gehindert.

Der Nutzen liegt damit nicht darin, dass ein Angriff unmöglich wird, sondern darin, dass er auffällt und eingedämmt werden kann. Die Erkennung verkürzt die Zeit bis zur Entdeckung drastisch und begrenzt so den möglichen Schaden pro Vorfall. Genau dieser Effekt geht als reduzierte Eintrittswahrscheinlichkeit in die ROSI-Rechnung ein (in der Kalkulation als Rückgang der jährlichen Eintrittsrate von 0,35 auf 0,08 abgebildet).

---

## 7. Betrieb und Wartung der Abwehrmaßnahmen

Ein IDS ist keine einmalige Einrichtung, sondern braucht laufende Pflege, damit es wirksam bleibt. Die Regelbasis von Suricata sollte regelmäßig aktualisiert werden, da ständig neue Angriffsmuster bekannt werden. Das geschieht über `sudo suricata-update` mit anschließendem Neustart des Dienstes; in einer produktiven Umgebung wird das üblicherweise automatisiert, etwa wöchentlich per Cronjob.

Ebenso müssen die erzeugten Alerts regelmäßig gesichtet werden, denn eine Meldung nützt nur, wenn sie auch jemand liest. Dafür werden die Logs in `/var/log/suricata/` beobachtet, wobei `fast.log` einen schnellen Überblick gibt und `eve.json` die vollständigen Details für eine genauere Auswertung enthält. Da diese Logs mit der Zeit anwachsen, sorgt eine Log-Rotation dafür, dass die Festplatte nicht vollläuft.

Die Firewall-Regeln und die statischen ARP-Einträge sind ebenfalls zu pflegen. Kommt ein neues, legitimes Gerät ins Netz, muss dessen MAC-Adresse in die Whitelist aufgenommen werden; ändert sich das Gateway, ist der statische ARP-Eintrag anzupassen. In der ROSI-Rechnung ist dieser laufende Aufwand als jährliche Betriebskosten für Monitoring und Regelpflege berücksichtigt.
