# Kurzbeschreibung

## Thema
WiFi Pineapple – Rogue Access Point Angriff & Abwehr

## Zielsetzung
Im Rahmen dieses Projekts simulieren wir zwei realistische WLAN-/Netzwerkangriffe und zeigen, wie diese durch geeignete Gegenmaßnahmen erkannt bzw. verhindert werden können:

- **Angriffsvektor 1 – Rogue Access Point (extern):** Mit dem WiFi Pineapple wird ein bekanntes WLAN (z. B. "HTW-Guest") simuliert. Opfer-Geräte verbinden sich automatisch, wodurch deren Traffic über den Angreifer läuft und mitgelesen werden kann.
- **Angriffsvektor 2 – ARP-Spoofing (intern):** Im internen Testnetz täuscht ein Kali-Linux-System vor, das Standard-Gateway zu sein, und leitet den Traffic des Opfers über sich selbst um.

Als Gegenmaßnahmen implementieren wir:
- **Suricata (IDS)** zur automatisierten Erkennung beider Angriffsmuster
- **Firewall-Regeln (iptables)** zur Blockierung unbekannter Geräte im Netz

Zusätzlich berechnen wir den wirtschaftlichen Nutzen der umgesetzten Schutzmaßnahmen anhand des ROSI (Return on Security Investment) und bereiten die Ergebnisse für eine Postersession sowie einen reproduzierbaren Abschlussbericht auf.

## Erwartete Ergebnisse
- Beide Angriffsvektoren funktionieren reproduzierbar und sind durch Screenshots, PCAPs und Logs belegt
- Suricata erkennt beide Angriffe zuverlässig und protokolliert entsprechende Alerts
- Firewall-Regeln blockieren erfolgreich unbekannte/unautorisierte Geräte im Testnetz
- Vorher/Nachher-Nachweise zeigen die Wirksamkeit der Gegenmaßnahmen klar und nachvollziehbar
- Eine plausibel begründete ROSI-Berechnung mit dokumentierten Annahmen liegt vor
- Ein vollständiger, reproduzierbarer Abschlussbericht sowie ein wissenschaftliches Poster (A0) liegen zur Abgabe vor

## Testumgebung (Kurzüberblick)
Simulierte Laborumgebung mit drei virtuellen Maschinen (Kali Linux als Angreifer, Windows als Opfer, Ubuntu Server mit Suricata) sowie dem vom Labor bereitgestellten WiFi Pineapple als Rogue-Access-Point-Hardware. Details zu Netzwerkdiagramm und IP-Plan siehe `/dokumentation`.

