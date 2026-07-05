# WiFi Pineapple – Grundlagen & Einrichtung

**Projekt:** WiFi Pineapple – Rogue AP & ARP-Spoofing (Projektaufgabe Nr. 2)  
**Verantwortlich:** Person B  
**Erstellt:** Woche 1  

---

## Was ist das WiFi Pineapple?

Das WiFi Pineapple ist ein spezialisiertes WLAN-Pentest-Gerät von Hak5.
Es sieht aus wie ein kleiner Router mit mehreren Antennen.
Kernfunktion: Es gibt sich als bekanntes WLAN aus und bringt Geräte dazu,
sich automatisch damit zu verbinden – ohne dass der Nutzer es merkt.
Das passiert über das eingebaute **PineAP Modul**.

---

## Schritt 1 – Gerät anschließen

1. WiFi Pineapple per **USB-C Kabel** an den Laptop stecken (den Laptop auf dem VirtualBox läuft)
2. Warten bis die LEDs stabil leuchten (ca. 30–60 Sekunden)
3. Windows erkennt das Gerät automatisch als neuen Netzwerkadapter
   → kein Treiber nötig, kein Download nötig

---

## Schritt 2 – IP-Adresse am Laptop setzen (Windows 11)

Das Pineapple hat eine feste Management-IP (`172.16.42.1`). Damit der Laptop
über den Browser darauf zugreifen kann, muss am Laptop eine passende IP gesetzt werden.

> **Hinweis:** Diese IP gilt nur für den Zugriff auf das Web-Interface des Pineapple —
> nicht zu verwechseln mit der Projekt-IP `192.168.30.5` die das Pineapple im Testnetz bekommt.

**Vorgehensweise unter Windows 11:**

1. Windows-Taste → "Netzwerk- und Interneteinstellungen" öffnen
2. Runterscrollen → "Erweiterte Netzwerkeinstellungen" → "Weitere Netzwerkadapteroptionen"
3. Im neuen Fenster: **WiFi Pineapple** Adapter finden
   (taucht nach dem Anschließen neu auf, heißt oft "ASIX USB Ethernet")
4. Rechtsklick → Eigenschaften
5. "Internetprotokoll Version 4 (TCP/IPv4)" auswählen → Eigenschaften
6. "Folgende IP-Adresse verwenden" aktivieren und eintragen:

| Feld | Wert |
|------|------|
| IP-Adresse | `172.16.42.42` |
| Subnetzmaske | `255.255.0.0` |
| Standardgateway | leer lassen |
| Bevorzugter DNS | `8.8.8.8` |
| Alternativer DNS | `8.8.4.4` |

7. OK klicken → Fenster schließen

---

## Schritt 3 – Ersteinrichtung (Setup-Wizard)

Da das Gerät beim ersten Start noch keine Firmware hat, startet automatisch
ein Setup-Assistent der durch die Einrichtung führt.

1. Browser öffnen (Chrome, Firefox, Edge)
2. Adresse eingeben: `http://172.16.42.1:1471`
   → **Wichtig:** der Port `:1471` muss dabei sein, sonst erscheint eine leere Seite
3. Setup-Wizard erscheint automatisch
4. Firmware herunterladen: Das Pineapple benötigt kurz Internetzugang.
   Es kann das WLAN des Laptops **nicht automatisch** mitnutzen.
   Zwei Optionen:
   - **Option A – Internet Connection Sharing (ICS):** Netzwerkeinstellungen →
     WLAN-Adapter → Rechtsklick → Eigenschaften → Reiter "Freigabe" →
     Haken bei "Anderen Netzwerkbenutzern erlauben..." → WiFi Pineapple Adapter auswählen → OK
   - **Option B (einfacher):** Handy-Hotspot aufmachen, Pineapple direkt damit verbinden
5. Root-Passwort setzen und sicher notieren — wird für alle späteren Logins benötigt
6. Nach abgeschlossenem Setup: `http://172.16.42.1:1471` erneut aufrufen
7. Login: Benutzername `root`, Passwort = das in Schritt 5 gesetzte

---

## Schritt 4 – Web-Interface verstehen

Nach dem Login erscheint das **Dashboard**. In der linken Sidebar befinden sich
alle relevanten Menüpunkte:

| Menüpunkt | Funktion |
|-----------|----------|
| **Dashboard** | Übersicht: CPU, RAM, verbundene Clients, SSIDs |
| **PineAP** | Kernmodul für den Rogue-AP Angriff |
| **Recon** | Scannt die Umgebung nach WLANs und Clients |
| **Clients** | Zeigt alle verbundenen Geräte |
| **Logging** | Logs und Protokolle |
| **Settings** | Netzwerk, Passwort, Updates |

---

## Schritt 5 – Open AP vs PineAP

**Open AP** (unter Settings → PineAP → Access Points):
- Sendet ein simples, offenes WLAN mit festem Namen (z.B. `Pineapple_1337`)
- Statisch – immer derselbe Netzwerkname
- Kein Passwort nötig
- Verwendung: kurzer Funktionstest ob das Gerät grundsätzlich arbeitet

**PineAP** (das eigentliche Angriffstool):
- Hört passiv welche WLANs Geräte in der Umgebung suchen
- Gibt sich dynamisch als genau diese bekannten Netze aus
- Lockt Geräte automatisch an die sich mit bekannten Netzen verbinden wollen
- Kann zusätzlich Deauth-Pakete senden um Geräte vom echten WLAN zu trennen

**Im Projekt wird PineAP verwendet** — es ist Angriffsvektor 1 (Rogue AP).
Open AP dient lediglich als initialer Funktionstest.

### Verbindung zu Suricata und Firewall

Suricata (IDS, `192.168.30.2`) überwacht den gesamten Traffic im Testnetz.
Sobald PineAP aktiv ist und ein Opfergerät sich verbindet, erkennt Suricata
das Angriffsmuster und erzeugt einen Alert-Log. Das ist der Vorher/Nachher-Nachweis
der für die Abgabe benötigt wird:

- PineAP aktiv → Suricata schlägt Alarm → Alert-Log gespeichert ✅
- Firewall-Regel aktiv → PineAP wird blockiert → kein Alert mehr ✅

### Ablauf im Labor (Woche 2)

| Schritt | Aktion |
|---------|--------|
| 1 | Pineapple einrichten (Schritte 1–4 dieser Anleitung) |
| 2 | Open AP kurz testen → Windows VM verbindet sich → Screenshot |
| 3 | PineAP aktivieren → SSID "HTW-Guest" in Pool → Broadcast an |
| 4 | Windows VM verbindet sich automatisch → unter "Clients" sichtbar |
| 5 | Wireshark auf Kali VM → Traffic mitschneiden → PCAP speichern |
| 6 | Suricata-Alert prüfen → Log sichern |
| 7 | Screenshots und PCAPs ins Git committen |

---

## Schritt 6 – SSID konfigurieren im PineAP Modul

**Was ist eine SSID?**
Die SSID ist der Netzwerkname der in der WLAN-Liste eines Geräts angezeigt wird
(z.B. "HTW-Guest", "Telekom_12345"). Geräte verbinden sich automatisch mit SSIDs
die sie bereits kennen — genau diese Schwachstelle nutzt PineAP aus.

**Konfiguration:**

1. Sidebar: **PineAP** anklicken
2. Reiter **"Advanced"** wählen
3. Folgende Optionen aktivieren:
   - **Allow Associations** → Geräte dürfen sich verbinden
   - **Capture SSIDs to Pool** → Pineapple sammelt SSIDs aus der Umgebung automatisch
   - **Broadcast SSID Pool** → Pineapple sendet alle SSIDs im Pool aktiv aus
4. Im SSID Pool rechts: SSID manuell eintragen (z.B. `HTW-Guest`) → **Add**
5. **Save** klicken

Das Pineapple sendet nun diese SSID — Geräte die dieses Netz kennen
verbinden sich automatisch.

---

## Schritt 7 – Pineapple ins Testnetz integrieren

**Warum ist das notwendig?**
Damit der Angriff funktioniert und Suricata ihn erkennen kann, müssen alle
Geräte im selben virtuellen Netz (`labnet`) erreichbar sein. Das Pineapple
muss daher nicht nur als Rogue AP nach außen senden, sondern auch als Gerät
im Testnetz registriert sein — sonst ist der Traffic für Suricata und Kali unsichtbar.

**IP-Plan des Projekts (verbindlich):**

| Gerät | Rolle | IP |
|-------|-------|-----|
| Router | Gateway | 192.168.30.1 |
| Suricata VM | IDS | 192.168.30.2 |
| **WiFi Pineapple** | **Rogue AP** | **192.168.30.5** |
| Kali VM | Angreifer | 192.168.30.10 |
| Windows VM | Opfer | 192.168.30.20 |

**Einbindung ins Testnetz:**

1. Settings → **Networking**
2. Unter "Wireless Client Mode" → **Scan** drücken
3. Labornetz (`labnet`) auswählen
4. Verbindung herstellen → **Connect**
5. Statische IP `192.168.30.5` manuell setzen
   (falls kein DHCP verfügbar: über das Web-Terminal im Pineapple)

---

## Systemarchitektur – Zusammenhänge

Das `labnet` ist ein rein virtuelles, isoliertes Netzwerk das lokal in VirtualBox
läuft. Es hat keine Verbindung ins Internet. Alle VMs und das Pineapple
kommunizieren ausschließlich innerhalb dieses Netzes.

Das Pineapple übernimmt dabei zwei Rollen gleichzeitig:

```
[Windows VM – Opfer, 192.168.30.20]
        |
        | verbindet sich mit fake SSID "HTW-Guest"
        ↓
[WiFi Pineapple – 192.168.30.5]
        |
        | ist Teil des labnet
        ↓
[labnet – isoliertes virtuelles Netz in VirtualBox]
        |                          |
        ↓                          ↓
[Suricata VM – 192.168.30.2]   [Kali VM – 192.168.30.10]
 überwacht Traffic,             führt ARP-Spoofing aus,
 erzeugt Alert-Logs             zeichnet PCAPs auf
```

- **Nach außen:** Pineapple sendet fake SSID → Windows VM wird angelockt
- **Nach innen:** Pineapple sitzt im labnet → Traffic ist für Suricata und Kali sichtbar

Das GitHub-Repo enthält ausschließlich Dokumentation, Skripte und Logs —
das labnet selbst existiert lokal in VirtualBox und nicht in der Cloud.

---

## Checkliste Ersteinrichtung (Woche 2 – Labortermin)

| # | Aufgabe | Erledigt |
|---|---------|----------|
| 1 | Pineapple per USB-C anschließen, LEDs abwarten | ☐ |
| 2 | IP `172.16.42.42` am Windows-Adapter setzen | ☐ |
| 3 | Browser → `http://172.16.42.1:1471` → Setup-Wizard durchlaufen | ☐ |
| 4 | Root-Passwort setzen, einloggen, Dashboard prüfen | ☐ |
| 5 | PineAP konfigurieren, SSID "HTW-Guest" hinzufügen | ☐ |
| 6 | Pineapple ins labnet einbuchen → IP `192.168.30.5` setzen | ☐ |
| 7 | Screenshots aller Schritte erstellen und committen | ☐ |

---

## Geplante Aktionen Woche 2

| Aktion | Beschreibung |
|--------|-------------|
| **Rogue AP Angriff** | Pineapple gibt sich als bekanntes WLAN aus → Windows VM verbindet sich automatisch → Angreifer sitzt zwischen Opfer und Netz |
| **ARP-Spoofing** | Kali VM täuscht der Windows VM vor der Router zu sein → gesamter Traffic des Opfers läuft über den Angreifer |
| **PCAP aufzeichnen** | Wireshark zeichnet den Netzwerktraffic vollständig auf und speichert ihn als Datei → dient als Abgabenachweis |

---

*Quelle: WiFi Pineapple Mark VII Dokumentation – [docs.hak5.org](https://docs.hak5.org/wifi-pineapple)*