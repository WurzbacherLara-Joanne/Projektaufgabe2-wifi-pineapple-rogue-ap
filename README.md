# WiFi Pineapple – Rogue AP & ARP-Spoofing: Angriff & Abwehr

Projektaufgabe Nr. 2 (Informationssicherheit) – Gruppe 1, HTW Berlin, Sommersemester 2026

> Die ausführliche Kurzbeschreibung mit Zielsetzung, erwarteten Ergebnissen und Teamzuteilung steht in `KURZBESCHREIBUNG.md`.

## Was wir machen

Wir simulieren zwei Angriffe auf WLAN und lokale Netzwerke in einer isolierten Testumgebung:
- **Rogue Access Point** (extern): Der WiFi Pineapple gibt sich als bekanntes WLAN aus, Geräte verbinden sich automatisch, der Traffic wird mitgeschnitten.
- **ARP-Spoofing** (intern): Kali Linux täuscht im internen Netz vor, das Gateway zu sein, und leitet den Traffic des Opfers um.

Ergänzend werden Schutzmaßnahmen (Suricata, arpwatch, Firewall, statische ARP-Einträge) konzeptionell erarbeitet und der wirtschaftliche Nutzen anhand einer ROSI-Berechnung bewertet.

Alle Tests finden ausschließlich in einer isolierten Laborumgebung statt – keine Tests gegen fremde Netzwerke.

---

## Testumgebung

Das Projekt verwendet zwei voneinander getrennte Netzwerke:

### Pineapple-Netz (Rogue-AP-Angriff)

| Gerät | Rolle | IP |
|-------|-------|----|
| WiFi Pineapple Mark VII | Rogue Access Point | 172.16.42.1 |
| Verwaltungs-Laptop (Dario) | Pineapple Web-Interface | 172.16.42.42 |
| Opfer-Endgerät | Verbundenes WLAN-Gerät | 172.16.42.154 (via Pineapple DHCP) |

### Internes Netz – labnet (ARP-Spoofing)

| Gerät | Rolle | IP |
|-------|-------|----|
| Kali Linux VM | Angreifer / Sniffer | 192.168.30.10 |
| Windows 10 VM | Opfer-PC | 192.168.30.20 |

---

## Ablauf

| Woche | Inhalt |
|-------|--------|
| 1 | Konzept, Git-Repo, Lab-Setup (VMs), IP-Konfiguration, Ping-Tests |
| 2 | Pineapple-Termin: Rogue-AP-Angriff + ARP-Spoofing, PCAPs sammeln |
| 3 | Gegenmaßnahmen theoretisch erarbeiten, ROSI berechnen |
| 4 | Bericht & Poster fertigstellen, Probepräsentation, Abgabe |

---



## Ordnerstruktur

```
Projektaufgabe2-wifi-pineapple-rogue-ap/
├── bericht/
│   ├── 01VM-Setup/
│   │   ├── suricata/
│   ├── 02Vorbereitung-Pineapple-Termin/
│   ├── 03Pineappleeinrichtung/
│   ├── werft_abbildungen/
│   ├── Abschlussbericht.md
│   └── kurzbeschreibung.md
├── pcaps/
│   ├── arpspoofing_01.pcap
│   └── rogue_ap_01.pcap
├── screenshots/
│   ├── 01VM-Setup/
│   ├── 02Vorbereitung-Pineapple-Termin/
│   ├── 03Pineappleeinrichtung/
│   ├── 04RogueAP-Angriff/
│   └── 05ARP-Spoofing/
├── skripte/
│   └── suricata/
│       ├── local.rules
│       └── suricata.yaml
├── CONTRIBUTING.md
└── README.md
```



---

## Team

| Name | Kürzel | Hauptverantwortung                       |
|------|--------|------------------------------------------|
| Lara | A | Lab-Setup, Netzwerk, VMs, Angriffe       |
| Dario | B | Angriffe, Pineapple, ARP-Spoofing, PCAPs |
| Jonas | C | Suricata IDS, Firewall, Gegenmaßnahmen   |
| Cellina | D | Dokumentation, ROSI, Bericht             |

---

## Abgabe

Der vollständige Abschlussbericht befindet sich unter `bericht/Abschlussbericht.md`.
PCAPs und Logs liegen in den jeweiligen Ordnern und sind Teil der Abgabe.

---

## Hinweise


- Alle Tests nur in isolierter Laborumgebung (HTW-Labor oder eigene Systeme)
- Keine Tests gegen fremde Netzwerke, Datenschutz beachten