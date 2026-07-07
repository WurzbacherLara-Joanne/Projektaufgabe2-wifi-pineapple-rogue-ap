Fortschritt Woche 1 – Person C (Suricata IDS)

Projekt: WiFi Pineapple – Rogue AP & ARP-Spoofing (Gruppe 1)
Datum: 06.07.2026
Bearbeiter: Person C
Aufgabenpaket Woche 1: Suricata-Recherche, Basis-Setup, erste Regeln


Was heute erledigt wurde

Die eigene IDS-VM ist aufgesetzt und Suricata läuft nachweislich – Erkennung und Logging funktionieren.


VM aufgesetzt: Ubuntu Server 24.04 LTS in UTM (Host: macOS, Apple Silicon), 2 GB RAM, 2 Cores, 20 GB, QEMU-Backend (Virtualize).

Netzwerk aktuell NAT/DHCP → VM-IP 192.168.64.2, Interface enp0s1.
Die feste Lab-IP 192.168.30.2 wird erst in Woche 2 beim gemeinsamen Netzaufbau vergeben.



Suricata installiert: Version 8.0.5 über die OISF-Stable-PPA.
Regelset geladen: ET Open Rules via suricata-update.
Config-Test bestanden: suricata -T → „Configuration successfully loaded".
Als Dienst eingerichtet: läuft über systemd (systemctl status suricata → active (running)), startet automatisch beim Boot.
Smoke-Test erfolgreich: Test-Request gegen testmynids.org ausgelöst, Suricata hat den erwarteten Alert erzeugt:

GPL ATTACK_RESPONSE id check returned root [Classification: Potentially Bad Traffic] [Priority: 2]

![Suricata Smoke-Test](https://github.com/WurzbacherLara-Joanne/Projektaufgabe2-wifi-pineapple-rogue-ap/blob/main/screenshots/01VM-Setup/suricata/suricata_smoketest.png)

Erkenntnisse / Notizen für den Bericht


af-packet: fanout not supported erscheint als Warnung – normal bei virtualisierten NICs (UTM), kein Fehler. Suricata lauscht mit einem Interface trotzdem korrekt.
Hintergrundstart: suricata -D bricht in dieser Umgebung ab; der saubere Weg ist der systemd-Dienst (systemctl restart suricata). Wird künftig so genutzt.
ARP-Spoofing (Angriffsvektor 2): Suricata ist IP-basiert und erkennt reines ARP-Spoofing (Layer 2) nur eingeschränkt. Geplant ist die Kombination mit arpwatch für die Layer-2-Erkennung. → für Woche 2/3.