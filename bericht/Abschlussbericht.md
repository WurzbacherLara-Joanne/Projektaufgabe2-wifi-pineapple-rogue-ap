# Abschlussbericht

## 1. Einleitung

### Thema & Motivation
Kabellose Netzwerke gehören mittlerweile zur Grundausstattung nahezu jedes Haushalts und Unternehmens, gerade deshalb sind sie ein beliebtes Ziel für Angreifer. Viele Endgeräte verbinden sich automatisch mit bekannten WLAN-Namen (SSIDs), ohne dass Nutzern dies bewusst wahrnehmen. Dieses Verhalten lässt sich gezielt ausnutzen: Mit einem WiFi Pineapple kann ein bekanntes Netz (z. B. `HTW-Guest`) vorgetäuscht werden, wodurch sich Opfergeräte automatisch verbinden und ihr gesamter Traffic über den Angreifer läuft. Ergänzend zeigt dieses Projekt mit ARP-Spoofing einen zweiten, internen Angriffsvektor, bei dem sich ein Angreifer im selben Netzwerksegment als Gateway ausgibt.

Motivation des Projekts ist es, beide Angriffsarten praktisch nachzustellen, ihre Erkennung durch ein IDS (`Suricata`) sowie ihre Verhinderung durch Firewall-Regeln zu demonstrieren und den wirtschaftlichen Nutzen dieser Gegenmaßnahmen anhand einer ROSI-Berechnung zu bewerten.

### Zielsetzung
Ziel ist die reproduzierbare Umsetzung von zwei realistischen Angriffsszenarien sowie der zugehörigen Gegenmaßnahmen:

- **Angriffsvektor 1 – Rogue Access Point (extern):** Mit dem WiFi Pineapple wird ein bekanntes WLAN simuliert, sodass sich Opfergeräte automatisch verbinden und ihr Traffic mitgelesen werden kann.
- **Angriffsvektor 2 – ARP-Spoofing (intern):** Ein Kali-Linux-System täuscht im internen Testnetz vor, das Standard-Gateway zu sein, und leitet den Traffic des Opfers um.

Als Gegenmaßnahmen werden Suricata (IDS) zur automatisierten Angriffserkennung sowie Firewall-Regeln (iptables) zur Blockierung unautorisierter Geräte eingesetzt. Abschließend wird der wirtschaftliche Nutzen der Schutzmaßnahmen mittels ROSI (Return on Security Investment) bewertet.

> Details zu den erwarteten Ergebnissen siehe `KURZBESCHREIBUNG.md`.

## 2. Testumgebung

### Netzwerkdiagramm
Es wurde ein isoliertes, internes VirtualBox-Netzwerk namens `labnet` genutzt, das weder Internetzugang noch eine Verbindung zum Host besitzt.

Der WiFi Pineapple übernimmt eine Doppelrolle: 
Einerseits sendet er nach außen die Fake-SSID, um das Opfer „anzulocken", andererseits ist er gleichzeitig regulärer Teilnehmer im `labnet`.

Der Traffic-Fluss stellt sich wie folgt dar: 
Das Windows-Opfer verbindet sich mit der vom Pineapple gesendeten Fake-SSID. Da der Pineapple Teil des `labnet` ist, wird dieser Traffic dort sichtbar. Suricata überwacht das `labnet` und erkennt so den Angriff, während die Kali-VM den Traffic innerhalb des `labnet` für ARP-Spoofing bzw. Sniffing nutzen kann.

### IP-Plan

| Gerät | Rolle | IP |
|-------|-------|-----|
| Router | Gateway | 192.168.30.1 |
| Suricata VM | IDS | 192.168.30.2 |
| WiFi Pineapple | Rogue AP | 192.168.30.5 |
| Kali VM | Angreifer | 192.168.30.10 |
| Windows VM | Opfer | 192.168.30.20 |

> **Zusatz:** Der Pineapple hat eine separate Management-IP (`172.16.42.1`, Laptop-Adapter `172.16.42.42`), welche nur für den Zugriff auf das Web-Interface genutzt wird.

### Eingesetzte Hardware/VMs
- **Host:** Windows 11, 16 GB RAM, VirtualBox
- **Kali-Angreifer:** 4096 MB RAM, 2 CPUs, 25 GB (Debian 64-bit, Desktop Xfce + Top10 Tools)
- **Ubuntu-Suricata:** 2048 MB RAM, 2 CPUs, 20 GB (Ubuntu Server, IDS-Host)
- **Windows-Opfer:** 4096 MB RAM, 2 CPUs, 50 GB (Windows 10, simuliertes Opfergerät)
- **WiFi Pineapple Mark VII (Hak5):** per USB-C an Host-Laptop, zusätzlich über das WLAN ins `labnet` eingeloggt


## 3. Angriffsvektor 1 – Rogue Access Point (extern)
- Vorgehen Schritt für Schritt
- Screenshots / PCAPs
- Ergebnis

## 4. Angriffsvektor 2 – ARP-Spoofing (intern)
- Vorgehen Schritt für Schritt
- Screenshots / PCAPs
- Ergebnis

## 5. Gegenmaßnahmen
- Suricata IDS – Konfiguration & Regeln
- Firewall (iptables) – Konfiguration
- Vorher/Nachher-Nachweis je Angriffsvektor

## 6. ROSI-Berechnung
- Formel & Methodik
- Annahmen (Asset-Wert, Eintrittswahrscheinlichkeiten, Kosten)
- Ergebnis & Interpretation
- Handlungsempfehlung

## 7. Fazit
- Zusammenfassung der Ergebnisse
- Lessons Learned

## Anhang
- Skripte
- Vollständige Logs
- Verzeichnis aller PCAPs/Screenshots