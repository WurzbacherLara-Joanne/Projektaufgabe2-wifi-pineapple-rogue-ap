# Projektplan

**Projekt:** WiFi Pineapple – Rogue Access Point Angriff & Abwehr 

**Modul:** Informationssicherheit

**Hochschule:** HTW Berlin

**Gruppe:** Gruppe 1

**Semester:** Sommersemester 2026

**Projektstart:** 30.06.2026 

**Abgabe/Vorstellung:** 28.07.2026


---

## Rollenverteilung

| Name | Hauptverantwortung |
|-|---|
| Lara-Joanne In der Mühle | Lab-Setup, Netzwerk, VMs, Angriffe |
| Dario Vujnovic | Angriffe, Pineapple, ARP-Spoofing, PCAPs |
| Jonas Wolf | Suricata IDS, Firewall, Gegenmaßnahmen |
| Cellina Usunow | Dokumentation, ROSI, Bericht |

---

## Zeitplan & Meilensteine

### Woche 1 (30.06.–07.07.2026): Konzept & Lab-Setup
**Meilenstein:** Lab-Umgebung läuft, Vorbereitung für den Pineapple-Termin abgeschlossen

| Aufgabe                                                           | Verantwortlich | Status |
|-------------------------------------------------------------------|---|------|
| Git-Repository anlegen, Ordnerstruktur, Namenskonventionen        | Lara | Done |
| Projektplan erstellen                                             | Lara | Done |
| Kurzbeschreibung schreiben                                        | Lara | Done    |
| VirtualBox installieren, ISOs herunterladen                       | Lara | Done    |
| VMs installieren und konfigurieren                                | Lara | Done    |
| IP-Konfiguration aller VMs, Ping-Tests                            | Lara | Done    |
| Wireshark & arpspoof auf Kali prüfen, IP-Forwarding konfigurieren | Lara | Done    |
| Pineapple-Dokumentation durcharbeiten                             | Dario | Done    |
| Suricata-Grundlagen recherchieren                                 | Jonas | Done    |
| Bericht-Gliederung und Doku-Struktur anlegen                      | Cellina | Done    |

### Woche 2 (07.07.2026): Pineapple-Termin
**Meilenstein:** Beide Angriffsvektoren durchgeführt, PCAPs gespeichert

| Aufgabe                                                | Verantwortlich | Status |
|--------------------------------------------------------|----------------|--|
| Pineapple eingerichtet                                 | Dario          | Done |
| Angriffe durchgeführt                                  | Dario + Lara   | Done |
| PCAP `rogue_ap_01.pcap` gespeichert                    | Dario          | Done |
| PCAP `arpspoofing_01.pcap` gespeichert                 | Dario          | Done |
| Suricata auf Kali installiert                          | Lara           | Done |
| Feststellung: Gegenmaßnahmen nur in der Theorie nötig! | Alle           | Done |
| Absprache mit Mevre                                    | Alle           | Done |
| Screenshots und PCAPs verarbeiten                      | Cellina        | Done |


### Woche 2 (08.07.–14.07.2026): Gegenmaßnahmen & ROSI
**Meilenstein:** Gegenmaßnahmen konzeptionell dokumentiert, ROSI berechnet

| Aufgabe                                                  | Verantwortlich | Status |
|----------------------------------------------------------|---|---|
| Suricata-Regeln für beide Angriffsvektoren erarbeiten    | Jonas | Done |
| arpwatch konzeptionell dokumentieren                     | Jonas | Done |
| Firewall-Konzept (iptables Whitelist) erarbeiten         | Jonas | Done |
| Weitere Gegenmaßnahmen dokumentieren                     | Jonas | Done |
| ROSI-Berechnung für SWDS Werft                           | Cellina | Done |
| Abschlussdokumentation schreiben (Erstentwurf)           | Cellina | Done |
| Netzwerksegmentierungs-Analyse (Pineapple-Netz ↔ labnet) | Dario | Done |

### 14.07.2026 – Kontrolltermin
Zwischenstand geprüft.

### Woche 3 (14.07.–21.07.2026) und Woche 4 (21.07.–28.07.2026): Dokumentation & Finalisierung
**Meilenstein:** Bericht und Präsentation abgabefertig

| Aufgabe                                          | Verantwortlich | Status |
|--------------------------------------------------|----------------|--------|
| Kontrolle der Angriffe und Screenshots am 14.07. | Alle           | Done |
| Abschlussbericht finalisieren                    | Cellina        | Done   |
| Offene Platzhalter im Bericht auflösen           | Alle           | Done     |
| Präsentation erstellen (10 Min)                  | Alle           | Done     |
| README finalisieren                              | Lara           | Done     |
| Abgabe-Checkliste durchgehen                     | Alle           | Done     |

### 28.07.2026 – Abgabe und Vorstellung

---

## Anmerkungen zum Projektverlauf

- Die Windows-VM wurde weder für den Rogue-AP-Test (kein WLAN-Adapter) noch für den ARP-Spoofing-Test verwendet. Beim Rogue-AP-Test kam ein Smartphone zum Einsatz, beim ARP-Spoofing-Test ein realer Windows-Laptop (siehe Abschnitt 2.2.4 in der Abschlussdokumentation).
- Die Ubuntu-VM für Suricata wurde installiert, aber im finalen Setup nicht aktiv genutzt 
- Suricata wird nach Absprache mit der Dozentin (Mevre Tunca, 07.07.2026) nur theoretisch behandelt.
