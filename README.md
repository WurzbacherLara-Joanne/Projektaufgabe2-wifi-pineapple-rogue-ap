# WiFi Pineapple – Rogue AP & ARP-Spoofing: Angriff & Abwehr

Projektaufgabe Nr. 2 (Informationssicherheit) – Gruppe 1


> Die ausführliche Kurzbeschreibung mit Zielsetzung, erwarteten Ergebnissen und Teamzuteilung steht in `KURZBESCHREIBUNG.md`.

## Was wir machen

Wir greifen unser Testnetz auf zwei Arten an (Rogue Access Point mit dem WiFi Pineapple und ARP-Spoofing) und zeigen danach, wie man sich mit Suricata (IDS) und einer Firewall dagegen schützt. Am Ende berechnen wir den wirtschaftlichen Nutzen der Schutzmaßnahmen (ROSI).

Alle Tests finden ausschließlich in einer isolierten Laborumgebung statt – keine Tests gegen fremde Netzwerke.

## Testumgebung

| Gerät | Rolle | IP |
|-------|-------|-----|
| Router | Gateway | 192.168.30.1 |
| Suricata VM | IDS | 192.168.30.2 |
| WiFi Pineapple | Rogue AP | 192.168.30.5 |
| Kali VM | Angreifer | 192.168.30.10 |
| Windows VM | Opfer | 192.168.30.20 |

## Ablauf (4 Wochen)

| Woche | Inhalt |
|---|---|
| 1 | Konzept, Lab-Setup (3 VMs), Angriffe vorbereiten und testen |
| 2 | 1. Pineapple-Termin (1,5h): Rogue-AP-Angriff, danach ARP-Spoofing wiederholen |
| 3 | 2. Pineapple-Termin (1,5h): Gegenmaßnahmen testen (Suricata + Firewall, Vorher/Nachher) |
| 4 | ROSI berechnen, Bericht & Poster fertigstellen, Probepräsentation, Abgabe |

## Ordnerstruktur

```
/skripte        – Konfigurationen, Befehle, Regeln
/pcaps          – Wireshark-Aufnahmen
/screenshots    – nach Woche sortiert
/logs           – Suricata-Alerts, Firewall-Logs
/bericht        – Abschlussbericht
/dokumentation  – Netzwerkdiagramm, IP-Plan
```

## Namen für Dateien

- **PCAPs:** `angriffstyp_##.pcap` (z. B. `rogue_ap_01.pcap`)
- **Screenshots:** `woche#_thema_schritt.png` (z. B. `woche2_rogueap_verbindung.png`)
- **Logs:** `tool_kontext_vorher|nachher.log` (z. B. `suricata_rogueap_nachher.log`)