# Fortschritt 07.07.2026 – Person C (Suricata IDS & Firewall)

**Projekt:** WiFi Pineapple – Rogue AP & ARP-Spoofing (Gruppe 1)
**Bearbeiter:** Person C
**Sitzung:** Vor-Ort-Termin, ca. 3h
**Aufgabenpaket:** Eigene Suricata-Regeln, arpwatch, Vorbereitung Woche 2

---

## Was heute erledigt wurde

- **Eigene Suricata-Regeln geschrieben** (`local.rules`, SID 1000001–1000012):
  - Rogue-AP / Klartext-HTTP: neverssl-Zugriff, Klartext-Credentials im POST, HTTP Basic Auth (R1–R3)
  - ARP / MITM: ARP-Frame-Sichtbarkeit, DNS-Anfrage sichtbar, DNS-Antwort auf Angreifer-IP (R4–R6)
- **Regeln laden fehlerfrei:** `suricata -T -c /etc/suricata/suricata.yaml -v` läuft grün durch, alle eigenen Regeln werden geladen (Screenshot vorhanden).
- **arpwatch installiert und aktiviert** (`systemctl enable --now arpwatch`) – zweite Erkennungsschicht für Layer-2-ARP-Spoofing.
- **Regeln in `suricata.yaml` eingebunden** (`rule-files:` → `local.rules`).
- **Dateien ins Repo übertragen und committet** (`suricata.yaml`, `local.rules`, `REGELN.md`, Screenshots).

## Korrekturen an den Regeln (dokumentiert für Reproduzierbarkeit)

- **`alert ether …` funktioniert in diesem Build nicht** („protocol ether cannot be used in a signature"). ARP-Sichtbarkeit stattdessen über IP-Ebene / decode-event gelöst.
- **`nocase` bei `http.host` ist redundant** (Warnung) – entfernt. Hostname-Buffer ist ohnehin case-insensitive.

## Wichtige Erkenntnisse für die weitere Arbeit

1. **VM hat aktuell keinen funktionierenden Internet-Ausgang.**
   `ping neverssl.com` löst den Namen auf (DNS ok), aber `100% packet loss` – ausgehende Pakete kommen nicht durch. Vermutlich UTM-Netzwerkkonfiguration (NAT/Shared Network).
   → **Für die eigentliche Aufgabe unkritisch**, da Suricata den Angriffstraffic im isolierten Laborn­etz analysiert, nicht das echte Internet.
   → **To-do mit Person A** beim gemeinsamen Netz-Setup (Netzwerkmodus der VM prüfen).

2. **Live-Test von R1 (neverssl) auf Woche 2 verschoben.**
   Grund: kein VM-Internet + zu wenig Testtraffic. Der belastbare Nachweis kommt mit **Person B's `rogue_ap_01.pcap`**, die garantiert sauberen HTTP-Klartext enthält (neverssl-Aufruf ist im Pineapple-Ablaufplan vorgesehen). Testkommando steht bereit:
   ```
   sudo suricata -r rogue_ap_01.pcap -S local.rules -l ./out/
   ```

3. **Suricata allein ist für reines ARP-Spoofing schwach** (Layer 2).
   → **arpwatch ist die Hauptschicht** für ARP-Erkennung (MAC↔IP-Wechsel). Suricata deckt die IP-/MITM-Folgen ab (DNS-Spoofing, Klartext-Traffic). So auch im Bericht begründen (dreischichtige Erkennung, siehe REGELN.md).

4. **Regeln müssen im Labor an echte IPs angepasst werden.**
   - R6 (DNS-Spoof): Hex-IP `|c0 a8 1e 0a|` auf die tatsächliche Angreifer-IP setzen.
   - R5 (DNS-Query): Testdomain eintragen.
   - Interface-Name in `suricata.yaml` (`af-packet:`) auf Labor-Interface setzen.

5. **Technische Merkposten:**
   - Interface der VM: `enp0s1`
   - Suricata läuft als systemd-Dienst, startet automatisch beim Boot.
   - Hintergrundstart über `suricata -D` bricht ab → immer `systemctl restart suricata` nutzen.
   - `af-packet: fanout not supported` ist nur eine Warnung (virtualisierte NIC), kein Fehler.

## Offene Punkte / Abstimmung

- **Person A:** VM-Netzwerk (Internet-Ausgang) prüfen; Position der Suricata-VM im Labornetz klären (bekommt sie gespiegelten Traffic / hängt sie am Gateway?).
- **Person B:** `rogue_ap_01.pcap` nach Termin 1 ins Repo committen → Grundlage für den echten Regel-Test.

## Nächste Schritte (Woche 2)

- Eigene Regeln offline gegen `rogue_ap_01.pcap` testen → Vorher-Nachweise sammeln.
- ARP-Spoofing-PCAP von B gegen arpwatch + Suricata testen.
- R5/R6 an echte Labor-IPs anpassen.
- Firewall-Konzept (iptables) für Woche 3 vorbereiten.
