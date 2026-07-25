# Kurzbeschreibung

## Thema
WiFi Pineapple – Rogue Access Point Angriff & Abwehr

## Zielsetzung
Im Rahmen dieses Projekts simulieren wir zwei realistische WLAN-/Netzwerkangriffe und ordnen ein, wie diese durch geeignete Gegenmaßnahmen erkannt bzw. verhindert werden könnten:

- **Angriffsvektor 1 – Rogue Access Point (extern):** Mit dem WiFi Pineapple wird ein bekanntes WLAN (`HTW-Guest`) simuliert. Ein Testgerät verbindet sich automatisch, wodurch dessen Traffic über den Angreifer läuft und mitgelesen werden kann.
- **Angriffsvektor 2 – ARP-Spoofing (intern):** Ein Kali-Linux-System täuscht vor, das Standard-Gateway zu sein, und leitet den Traffic des Opfers über sich selbst um.

Als Gegenmaßnahmen ordnen wir konzeptionell ein:
- **Suricata (IDS)** zur Erkennung beider Angriffsmuster
- **Firewall-Regeln (iptables)**, arpwatch und feste ARP-Zuordnungen

Diese Schutzmaßnahmen werden ausgearbeitet und begründet, aber nicht durch eigene praktische Tests belegt (siehe Abschlussbericht Kapitel 6). Zusätzlich berechnen wir den wirtschaftlichen Nutzen anhand des ROSI (Return on Security Investment) und bereiten die Ergebnisse für eine Postersession sowie den Abschlussbericht auf.

## Erwartete Ergebnisse
- Beide Angriffsvektoren funktionieren reproduzierbar und sind durch Screenshots und PCAPs belegt
- Für beide Angriffsvektoren liegt eine begründete, konzeptionelle Einordnung wirksamer Gegenmaßnahmen vor
- Eine plausibel begründete ROSI-Berechnung mit dokumentierten Annahmen liegt vor
- Ein vollständiger, reproduzierbarer Abschlussbericht sowie ein wissenschaftliches Poster (A0) liegen zur Abgabe vor

## Testumgebung (Kurzüberblick)
Für den Aufbau wurden ursprünglich drei virtuelle Maschinen geplant (Kali Linux als Angreifer, Windows als Opfer, Ubuntu Server mit Suricata). Im Projektverlauf zeigte sich, dass nur die Kali-VM für die eigentlichen Angriffe benötigt wurde: Beim Rogue-Access-Point-Test kam ein echtes Smartphone als Zielgerät zum Einsatz (VMs haben keinen echten WLAN-Adapter), beim ARP-Spoofing-Test ein realer Windows-Laptop. Die Ubuntu-VM wurde aufgebaut, aber nicht aktiv genutzt, da Suricata nur theoretisch behandelt wird. Details siehe Abschlussbericht Abschnitt 2.2.4. Als externe Hardware diente zusätzlich der vom Labor bereitgestellte WiFi Pineapple. Details zu Netzwerkdiagramm und IP-Plan siehe `/dokumentation`.