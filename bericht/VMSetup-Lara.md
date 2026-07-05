# VM-Setup Dokumentation
## Woche 1 – Lab-Setup 

---

## 1. VirtualBox Installation

**Datum:** Woche 1  
**System:** Windows 11, 16 GB RAM

VirtualBox wurde von der offiziellen Seite heruntergeladen und installiert:
- Download: https://www.virtualbox.org/wiki/Downloads → "Windows hosts"
- Installation als Administrator ausgeführt (Rechtsklick → "Als Administrator ausführen")
- Alle Standardeinstellungen beibehalten
- Netzwerkadapter-Installation während Setup bestätigt

**Hinweis:** Erste Installation ohne Administratorrechte führte zu unvollständiger Netzwerktreiber-Installation (nur NAT und Netzwerkbrücke verfügbar statt alle Adapter-Typen). Fix: Neuinstallation als Administrator.

---

## 2. ISO-Images herunterladen

Folgende ISO-Dateien wurden heruntergeladen und unter `C:\Users\LaraWurzbach\Documents\VMs\ISOs` gespeichert:

| Betriebssystem | Quelle | Dateiname |
|----------------|--------|-----------|
| Kali Linux | https://www.kali.org/get-kali/#kali-installer-images (64-bit Installer) | kali-linux-2026.2-installer-amd64.iso |
| Ubuntu Server 22.04 LTS | https://ubuntu.com/download/server | ubuntu-22.04-live-server-amd64.iso |
| Windows 10 | https://www.microsoft.com/de-de/software-download/windows10 | Win10_German_x64.iso |

---

## 3. Netzwerk-Konfiguration

Vor der VM-Erstellung wurde ein internes Netzwerk für alle VMs konfiguriert:

- In VirtualBox: Datei → Werkzeuge → Netzwerk-Manager → "+" (neuen Adapter anlegen)
- Netzwerk-Name: **labnet**
- Typ: **Internes Netzwerk**

Alle VMs werden mit diesem internen Netzwerk verbunden, sodass sie untereinander kommunizieren können, aber vom Host-System und Internet isoliert sind.

**IP-Plan (verbindlich für alle VMs):**

| Gerät | Rolle | IP-Adresse |
|-------|-------|------------|
| Legitimer Router | Gateway | 192.168.30.1 |
| Suricata VM | IDS / Monitoring | 192.168.30.2 |
| WiFi Pineapple | Rogue Access Point | 192.168.30.5 |
| Kali Linux VM | Angreifer / Sniffer | 192.168.30.10 |
| Windows 10 VM | Opfer-PC | 192.168.30.20 |

---

## 4. Kali Linux VM erstellen und installieren

### 4.1 VM-Erstellung in VirtualBox

| Einstellung | Wert                                  |
|------------|---------------------------------------|
| VM-Name | Kali-Angreifer                        |
| Ordner | C:\Users\LaraWurzbacher\Documents\VMs  |
| ISO-Image | kali-linux-2026.2-installer-amd64.iso |
| Betriebssystem | Linux                                 |
| Distribution | Debian (64-bit)                       |
| RAM | 4096 MB                               |
| CPUs | 2                                     |
| Festplatte | 25,00 GB (VDI, dynamisch alloziert)   |

**Netzwerk-Einstellung der VM:**
- Adapter 1: Internes Netzwerk → **labnet**

### 4.2 Kali Linux Installation

Installer gestartet über: VM starten → **"Install"** (Text-Installer)

Installationsschritte:

| Schritt | Auswahl |
|---------|---------|
| Sprache | English |
| Location | Germany |
| Locale | en_US.UTF-8 |
| Keyboard | German |
| Netzwerk | Do not configure the network at this time (Option 5) |
| Hostname | kali-angreifer |
| Domain | *(leer)* |
| Benutzer (Full name) | kali |
| Benutzername | kali |
| Passwort | *(Gruppenpasswort, intern bekannt)* |
| Partitionierung | Guided – use entire disk |
| Partitionsschema | All files in one partition |
| Partitionierung bestätigen | Yes (Write changes to disk) |
| Software-Auswahl | Desktop environment (Xfce), top10 tools, default tools |
| GRUB Bootloader | Yes, auf /dev/sda installiert |

**Installation erfolgreich abgeschlossen. ✅**
![VM: Kali-Angreifer](screenshots/VM-Setup/VM-Kali/Kali-Angreifer-Einstellungen.png)
![Kali Desktop nach Installation](screenshots/VM-Setup/VM-Kali/VM-Kali-Angreifer-Startbildschirm.png)

**Bekannte Probleme & Lösungen:**

| Problem | Ursache | Lösung |
|---------|---------|--------|
| Nur NAT und Netzwerkbrücke als Netzwerkoptionen verfügbar | VirtualBox nicht als Administrator installiert | Deinstallation + Neuinstallation als Administrator |
| Festplattengröße 26 MB statt 25 GB | Schieberegler hat nicht korrekt gespeichert | VM gelöscht, Größe beim Neuerstellen direkt ins Zahlenfeld eingetippt |
| Graphical Installer friert bei "Starting up the partitioner" ein | Bekanntes Stabilitätsproblem des Graphical Installers in VMs | Wechsel auf Text-Installer ("Install" statt "Graphical Install") |

### 4.3 Snapshot erstellen

Nach erfolgreicher Installation wurde ein Snapshot als Backup angelegt:
- VirtualBox: Rechtsklick auf Kali-Angreifer → Snapshots → Aufnehmen
- Snapshot-Name: **frische-installation**
- Zweck: Wiederherstellungspunkt vor invasiven Tests

---

## Nächste Schritte (noch ausstehend)

- [x] Kali Linux Installation abschließen ✅
- [x] Screenshots erstellt ✅
- [ ] Manuelle IP-Konfiguration Kali: 192.168.30.10
- [ ] Windows 10 VM erstellen und installieren (IP: 192.168.30.20)
- [ ] Ubuntu Server VM erstellen und installieren (IP: 192.168.30.2, für Suricata)
- [ ] Ping-Tests zwischen allen VMs durchführen und dokumentieren
- [ ] VM-Snapshots für Windows und Ubuntu erstellen