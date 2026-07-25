# Contributing

## PCAPs
`angriffstyp_##.pcap`
Beispiele: `rogue_ap_01.pcap`, `arpspoofing_01.pcap`
> Hinweis: PCAP-Dateien werden in dieser Abgabe nicht eingereicht – auf Anweisung der Dozentin aus Datenschutzgründen. Der Ordner `/pcaps` bleibt leer bzw. entfällt.

## Screenshots
`thema_schritt.png`
Beispiel: `rogueap_verbindung.png`
Falls mehrere Versionen vorhanden sind:
`thema_schritt_##.png`
Beispiel: `rogueap_verbindung_01.png`, `rogueap_verbindung_02.png`

## Logs
`tool_kontext_vorher|nachher.log`
Beispiel: `suricata_rogueap_nachher.log`

> Hinweis: Für dieses Projekt werden keine Logs eingereicht, da Suricata und die Firewall-Regeln nur konzeptionell behandelt und nicht praktisch getestet wurden (siehe Abschlussbericht Kapitel 6). Der Ordner `/logs` bleibt aktuell leer bzw. entfällt in dieser Abgabe.

## Ordnerstruktur

    /skripte        – Konfigurationen, Befehle, Regeln
    /pcaps          – in dieser Abgabe nicht vorhanden
    /screenshots    – alle Screenshots
    /logs           – in dieser Abgabe nicht befüllt, da Schutzmaßnahmen nur theoretisch behandelt wurden
    /vms            – nicht im Repo enthalten (Dateigröße ca. 7 GB); Kali-Angreifer-VM extern verfügbar: https://drive.google.com/drive/folders/1Rvroc2MBLuCCu-NloN5FfeESErcF5Laf?usp=sharing
    /bericht        – Abschlussbericht
    /dokumentation  – Netzwerkdiagramm, IP-Plan