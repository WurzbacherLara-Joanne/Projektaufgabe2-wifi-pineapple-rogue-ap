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

### 6.1 Ausgangslage: Angriffsfläche der SWDS Werft

Die Bewertung bezieht sich auf die reale Beispielorganisation "SWDS Werft" (siehe `Informationen Werft.pdf`), auf deren Netzplan und IT-Systemliste die im Projekt nachgestellten Angriffsvektoren übertragen wurden. Relevante Fakten aus der Dokumentation:

| Fakt | Quelle | Relevanz für die Angriffsvektoren |
|---|---|---|
| 10 WLAN-Access-Points (3 Betrieb, 7 Produktion) | Abb. 5 Netzplan | Angriffsfläche für Rogue-AP (Vektor 1) – Notebooks/iPads verbinden sich automatisch mit bekannten SSIDs |
| 17 IoT-Kameras im gemeinsamen Segment | Abb. 5 Netzplan | Häufig schwache/default Zugangsdaten, zusätzliche Fläche für Lateral Movement nach Erstzugriff |
| Keine IDS/IPS im Einsatz, nur ZyWALL-Firewalls | Abb. 4 IT-Systemliste | Angriffe (Rogue AP, ARP-Spoofing) würden aktuell unbemerkt bleiben |
| Windows Server 2012 R2 (Produktion) | Abb. 4 IT-Systemliste | Extended Support seit 10.10.2023 ausgelaufen → keine Sicherheitsupdates mehr |
| CNC-Fräse mit Windows 7 Pro | Abb. 4 IT-Systemliste | End-of-Life seit 2020, besonders kritisch, da Kernprozess "Fertigung" betroffen |
| Fertigung = Kernprozess der Werft | Abb. 2 Geschäftsprozesse | Ausfall der Produktions-IT trifft den Hauptumsatz direkt |
| Konstruktionsdaten (Delftship), Buchhaltungssystem, AD+DNS | Abb. 4 IT-Systemliste | Hoher Wert bei Diebstahl/Manipulation (IP-Schutz, Finanzdaten) |

Zusammengenommen ergibt sich ein plausibles Bild: unautorisierte Geräte (Rogue AP) oder ein Angreifer im internen Netz (ARP-Spoofing) könnten unbemerkt Traffic mitlesen bzw. umleiten und von dort auf veraltete, ungepatchte Systeme in der Produktion vordringen.

### 6.2 Annahmen

Die PDF liefert keine finanziellen Kennzahlen (Umsatz, konkrete Vorfallkosten). Diese wurden daher realistisch für eine Werft dieser Größenordnung (~30 Mitarbeitende, mittelständisch) geschätzt und unten transparent begründet. Alle Werte sind Schätzungen zur Veranschaulichung der Methodik, keine belastbaren versicherungsmathematischen Werte.

**Schadenshöhe pro erfolgreichem Vorfall (Single Loss Expectancy, SLE)** – angenommen wird ein Vorfall, bei dem über Rogue-AP oder ARP-Spoofing Zugangsdaten abgegriffen und zur weiteren Kompromittierung von Produktions- oder Betriebssystemen genutzt werden:

| Schadensposition | Betrag | Begründung |
|---|---|---|
| IT-Wiederherstellung & Forensik | 25.000 € | Neuaufsetzen betroffener Server/Clients (u. a. Server 2012 R2), externe Forensik |
| Produktionsausfall (Fertigung = Kernprozess) | 40.000 € | ca. 3 Tage Stillstand von CNC-Fräse/Gussmaschine bei Absicherung/Neuaufsetzen |
| Diebstahl/Manipulation Konstruktionsdaten (Delftship) | 50.000 € | Wettbewerbsnachteil, Auftraggeber-Pläne, konservativ geschätzter IP-Wert |
| Reputationsschaden/Kundenverlust | 20.000 € | Vertrauensverlust bei Auftraggebern (Vertrieb/Auftragsannahme betroffen) |
| Mögliche DSGVO-Bußgelder | 15.000 € | Personal- und Kundendaten (Personalabteilung, Vertrieb) betroffen |
| **Summe SLE** | **150.000 €** | |

**Eintrittswahrscheinlichkeit (Annual Rate of Occurrence, ARO):**

- **Vorher (ohne IDS/Firewall-Regeln):** 0,35 (35 % Wahrscheinlichkeit pro Jahr für einen erfolgreichen Vorfall über einen der beiden Vektoren). Begründung: 10 unüberwachte WLAN-APs, 17 IoT-Kameras, flaches Netz ohne Monitoring, zwei EOL-Systeme (Server 2012 R2, Win7 CNC) erhöhen sowohl die Angriffsfläche als auch die Erfolgswahrscheinlichkeit deutlich.
- **Nachher (mit Suricata IDS + iptables-Firewallregeln):** 0,08 (8 %). Begründung: Im Projekt wurden beide Angriffsvektoren zuverlässig durch Suricata erkannt (siehe Abschnitt 5); iptables-Regeln blockieren unautorisierte MAC-Adressen/Geräte direkt. Ein Restrisiko bleibt (z. B. sehr gezielte/neuartige Angriffe, Regel-Umgehung), daher keine 0 %.

**Kosten der Gegenmaßnahmen (Suricata + iptables):**

| Position | Einmalig | Jährlich | Begründung |
|---|---|---|---|
| Hardware (IDS-Host) | 800 € | – | kleiner dedizierter Server, wie im Testaufbau (Ubuntu-Suricata-VM) |
| Suricata (Lizenz) | 0 € | 0 € | Open Source |
| Implementierung & Regel-Tuning | 3.200 € | – | 40 h × 80 €/h Admin-/Consulting-Satz |
| Firewall-Regeln (iptables) einrichten | 640 € | – | 8 h × 80 €/h |
| Betrieb: Alert-Monitoring & Regel-Updates | – | 8.000 € | ca. 2 h/Woche × 52 Wochen × 80 €/h |
| **Summe Jahr 1** | **12.640 €** | | |
| **Summe Folgejahre** | | **8.000 €** | nur laufender Betrieb |

### 6.3 EAL/ALE-Berechnung vorher/nachher

$$ALE = ARO \times SLE$$

| | ARO | SLE | ALE (jährlicher Schadenserwartungswert) |
|---|---|---|---|
| **Vorher** | 0,35 | 150.000 € | **52.500 €** |
| **Nachher** | 0,08 | 150.000 € | **12.000 €** |

Risikoreduktion:

$$RiskReduction = \frac{ALE_{vorher} - ALE_{nachher}}{ALE_{vorher}} = \frac{52.500 - 12.000}{52.500} \approx 77{,}1\%$$

### 6.4 ROSI-Berechnung

$$ROSI = \frac{RiskReduction \times AssetValue - Cost}{Cost}$$

Dabei entspricht `AssetValue` dem jährlichen Schadenserwartungswert vor Maßnahmen (ALE<sub>vorher</sub> = 52.500 €).

**Jahr 1 (inkl. Einmalkosten für Anschaffung/Implementierung):**

$$ROSI = \frac{0{,}771 \times 52.500 - 12.640}{12.640} = \frac{40.478 - 12.640}{12.640} \approx 2{,}20 \;(\hat{=} 220\%)$$

**Ab Jahr 2 (nur laufende Betriebskosten):**

$$ROSI = \frac{40.478 - 8.000}{8.000} \approx 4{,}06 \;(\hat{=} 406\%)$$

Bereits im ersten Jahr übersteigt die eingesparte Risikosumme (≈ 40.478 €) die Investition (12.640 €) um mehr als das Dreifache – die Maßnahme amortisiert sich rechnerisch innerhalb des ersten Jahres.

### 6.5 Interpretation & Handlungsempfehlung

Die Investition in Suricata (IDS) und iptables-Firewallregeln ist wirtschaftlich klar vorteilhaft: Für eine jährliche Investition von 8.000 € (nach einmaliger Einrichtung für 12.640 € im ersten Jahr) wird das jährliche Schadensrisiko um rund 77 % bzw. 40.500 € reduziert. Der ROSI von 220 % im Einführungsjahr und über 400 % in den Folgejahren liegt deutlich über dem, was mit klassischen Kapitalinvestitionen erreichbar wäre.

**Empfehlung an die Geschäftsführung:**

> Sehr geehrter Herr Meissen,
> unsere Analyse zeigt, dass die Werft über 10 unüberwachte WLAN-Access-Points sowie veraltete, nicht mehr unterstützte Systeme in der Produktion (Server 2012 R2, Windows 7 auf der CNC-Fräse) verfügt. Ein Angreifer könnte – wie in unserem praktischen Test demonstriert – über einen gefälschten Access Point oder ARP-Spoofing im internen Netz unbemerkt Zugangsdaten abgreifen und von dort in die Produktionssysteme vordringen. Da die Fertigung unser Kernprozess ist, wäre bereits ein kurzer Ausfall wirtschaftlich spürbar.
> Mit einer Investition von rund 12.600 € im ersten Jahr (danach 8.000 €/Jahr) für ein Open-Source-Intrusion-Detection-System (Suricata) und angepasste Firewallregeln lässt sich das jährliche Risiko um etwa 77 % bzw. 40.500 € senken – ein Return on Security Investment von über 200 % bereits im ersten Jahr. Wir empfehlen die Umsetzung als kurzfristige, kosteneffiziente Maßnahme, bevor eines der beiden demonstrierten Angriffsszenarien in der Praxis ausgenutzt wird.

**Hinweis zur Belastbarkeit:** Die verwendeten Geldbeträge und Wahrscheinlichkeiten sind begründete Schätzungen (siehe 6.2), da die Ausgangsdokumentation keine Finanzkennzahlen enthält. Für eine produktive Entscheidungsgrundlage sollten ARO und SLE mit der Geschäftsführung/CISO anhand realer Vorfallhistorie bzw. Branchenbenchmarks (z. B. BSI-Lagebericht, Cyberversicherungs-Statistiken) validiert werden. Die grundsätzliche Schlussfolgerung – geringe Kosten stehen einem hohen Risikoreduktionspotenzial gegenüber – ist jedoch robust gegenüber moderaten Abweichungen dieser Annahmen.

## 7. Fazit
- Zusammenfassung der Ergebnisse
- Lessons Learned

## Anhang
- Skripte
- Vollständige Logs
- Verzeichnis aller PCAPs/Screenshots