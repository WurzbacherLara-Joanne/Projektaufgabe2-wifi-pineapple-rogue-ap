# VM-Setup Dokumentation
## Woche 1 – Lab-Setup 
**Datum:** Woche 1 (04.07.2026 & 05.07.2026)
**System:** Windows 11, 16 GB RAM

---

## 1. VirtualBox Installation

Die virtuellen Maschinen (VMs) bilden die Testumgebung für das Projekt. Anstatt echte Hardware zu gefährden oder fremde Netzwerke zu benutzen, simulieren wir alle Angriffe innerhalb dieser kontrollierten Umgebung. Kali Linux dient als Angreifer-System, Windows 10 als Opfer-PC und Ubuntu Server als IDS-Monitoring-System mit Suricata. 
Zusammen bilden sie ein realistisches Netzwerkszenario, das vollständig auf einem einzigen Laptop läuft.

VirtualBox wurde von der offiziellen Seite heruntergeladen und installiert:
- Download: https://www.virtualbox.org/wiki/Downloads 
- Installation als Administrator ausgeführt 
- Alle Standardeinstellungen beibehalten
- Netzwerkadapter-Installation während Setup bestätigt

**Hinweis:** Erste Installation ohne Administratorrechte führte zu unvollständiger Netzwerktreiber-Installation (nur NAT und Netzwerkbrücke verfügbar statt alle Adapter-Typen). Fix: Neuinstallation als Administrator.

---

## 2. ISO-Images herunterladen

ISO-Images sind digitale Abbilder einer Installations-CD. Sie enthalten alles was benötigt wird um ein Betriebssystem in einer VM zu installieren. 

Folgende ISO-Dateien wurden heruntergeladen und unter `C:\Users\LaraWurzbach\Documents\VMs\ISOs` gespeichert:

| Betriebssystem | Quelle | Dateiname                             |
|----------------|--------|---------------------------------------|
| Kali Linux | https://www.kali.org/get-kali/#kali-installer-images (64-bit Installer) | kali-linux-2026.2-installer-amd64.iso |
| Ubuntu Server 22.04 LTS | https://ubuntu.com/download/server | ubuntu-26.04-live-server-amd64.iso    |
| Windows 10 | https://www.microsoft.com/de-de/software-download/windows10 | Windows.iso                           |

---

## 3. Netzwerk-Konfiguration

Vor der VM-Erstellung wurde ein internes Netzwerk für alle VMs konfiguriert:

- In VirtualBox: Datei - Werkzeuge - Netzwerk-Manager - "+" (neuen Adapter anlegen)
- Netzwerk-Name: **labnet**
- Typ: **Internes Netzwerk**

Alle VMs werden mit diesem internen Netzwerk verbunden, sodass sie untereinander kommunizieren können, aber vom Host-System und Internet isoliert sind.

Für die Installation von Software (z.B. Wireshark) wurde der Netzwerkadapter einzelner VMs temporär auf NAT umgestellt, um Internetzugang zu ermöglichen. 
Nach der Installation wurde der Adapter jeweils wieder auf labnet zurückgestellt. Dabei war zu beachten, dass die manuell gesetzte IP-Adresse nach dem Wechsel zurück zu labnet neu gesetzt werden musste, da NAT eine andere IP vergibt.


**Geplanter IP-Plan (verbindlich für alle VMs):**

| Gerät | Rolle | IP-Adresse |
|-------|-------|------------|
| Legitimer Router | Gateway | 192.168.30.1 |
| Suricata VM | IDS / Monitoring | 192.168.30.2 |
| WiFi Pineapple | Rogue Access Point | 192.168.30.5 |
| Kali Linux VM | Angreifer / Sniffer | 192.168.30.10 |
| Windows 10 VM | Opfer-PC | 192.168.30.20 |

###

**Späterer finaler IP-Plan:**

Ursprünglich war geplant, drei VMs einzusetzen:

- Kali Linux (Angreifer), 
- Windows 10 (Opfer) und 
- Ubuntu Server (Suricata IDS). 

Im Projektverlauf (am 07.07.2026) stellte sich jedoch heraus, dass die Windows-VM für den Rogue-AP-Angriff nicht geeignet ist, da VMs keinen echten WLAN-Adapter haben und sich daher nicht mit dem WiFi Pineapple verbinden können. Als Opfer-Gerät wurde deshalb ein echter Windows-11-Laptop verwendet. 
Die Ubuntu-VM wurde ebenfalls aus dem finalen Setup entfernt, da Suricata im Rahmen des Projekts nur theoretisch behandelt wird (Herausstellung am 07.07.2026 **mit Absegnung** der Dozentin Mevre Tunca). Das Projekt läuft final mit einer VM (Kali Linux).


| Gerät                       | Rolle | IP-Adresse |
|-----------------------------|-------|------------|
| Legitimer Router            | Gateway | 192.168.30.1 |
| WiFi Pineapple              | Rogue Access Point | 192.168.30.5 |
| Kali Linux VM               | Angreifer / Sniffer | 192.168.30.10 |
| Opfer-Endgerät  | Opfer-Gerät | 172.16.42.x (via Pineapple DHCP) |


---

## 4. Kali Linux VM erstellen und installieren

### 4.1 VM-Erstellung in VirtualBox
**gerne nochmal überprüfen von Screenshot, dass Einstellungen stimmen**

| Einstellung | Wert                                  |
|------------|---------------------------------------|
| VM-Name | Angreifer-Kali                        |
| Ordner | C:\Users\LaraWurzbacher\Documents\VMs |
| ISO-Image | kali-linux-2026.2-installer-amd64.iso |
| Betriebssystem | Linux                                 |
| Distribution | Debian (64-bit)                       |
| RAM | 4096 MB                               |
| CPUs | 2                                     |
| Festplatte | 25,00 GB (VDI, dynamisch alloziert)   |


**Netzwerk-Einstellung der VM:**
- Adapter 1: Internes Netzwerk - **labnet**

###

### 4.2 Kali Linux Installation

Installer gestartet über: VM starten - **"Install"** (Text-Installer)

Installationsschritte:

| Schritt | Auswahl                                                |
|---------|--------------------------------------------------------|
| Sprache | English                                                |
| Location | Germany                                                |
| Locale | en_US.UTF-8                                            |
| Keyboard | German                                                 |
| Netzwerk | Do not configure the network at this time (Option 5)   |
| Hostname | kali-angreifer                                         |
| Domain | *(leer)*                                               |
| Benutzer (Full name) | kali                                                   |
| Benutzername | kali                                                   |
| Passwort | *(Gruppenpasswort, intern bekannt)*                    |
| Partitionierung | Guided - use entire disk                               |
| Partitionsschema | All files in one partition                             |
| Partitionierung bestätigen | Yes (Write changes to disk)                            |
| Software-Auswahl | Desktop environment (Xfce), top10 tools, default tools |
| GRUB Bootloader | Yes, auf /dev/sda installiert                          |

**!! Installation erfolgreich abgeschlossen. !!**

BILDER RICHTIG EINFÜGEN

![VM: Kali-Angreifer](https://github.com/WurzbacherLara-Joanne/Projektaufgabe2-wifi-pineapple-rogue-ap/blob/main/screenshots/01VM-Setup/VM-Kali/kali_angreifer_einstellungen.png)

![Kali Desktop nach Installation](https://github.com/WurzbacherLara-Joanne/Projektaufgabe2-wifi-pineapple-rogue-ap/blob/main/screenshots/01VM-Setup/VM-Kali/kali_angreifer_startbildschirm.png)

###

**Bekannte Probleme & Lösungen:**

| Problem | Ursache | Lösung |
|---------|---------|--------|
| Nur NAT und Netzwerkbrücke als Netzwerkoptionen verfügbar | VirtualBox nicht als Administrator installiert | Deinstallation + Neuinstallation als Administrator |
| Festplattengröße 26 MB statt 25 GB | Schieberegler hat nicht korrekt gespeichert | VM gelöscht, Größe beim Neuerstellen direkt ins Zahlenfeld eingetippt |
| Graphical Installer friert bei "Starting up the partitioner" ein | Bekanntes Stabilitätsproblem des Graphical Installers in VMs | Wechsel auf Text-Installer ("Install" statt "Graphical Install") |

---

## 5. Ubuntu Server VM erstellen und installieren

Auch wenn wir diese VM für den späteren Projektverlauf nicht mehr brauchen, war die Installation dennoch Teil des Projektes, weswegen wir uns dafür entschiedne haben, hier die Dokumentation drin zu lassen.

### 5.1 VM-Erstellung in VirtualBox

| Einstellung | Wert |
|------------|------|
| VM-Name | Ubuntu-Suricata |
| Ordner | C:\Users\LaraWurzbacher\Documents\VMs |
| ISO-Image | ubuntu-26.04-live-server-amd64.iso |
| Betriebssystem | Linux |
| Version | Ubuntu (64-bit) |
| RAM | 2048 MB |
| CPUs | 2 |
| Festplatte | 20,00 GB (VDI, dynamisch alloziert) |
| Benutzername | suricata |
| Passwort | *(Gruppenpasswort, intern bekannt)* |
| Hostname | Ubuntu-Suricata |

**Netzwerk-Einstellung der VM:**
- Adapter 1: Internes Netzwerk - **labnet**

### 5.2 Installation

Unbeaufsichtigte Installation über VirtualBox automatisch durchgeführt.
Login nach Installation erfolgreich getestet mit Benutzer `suricata`.

BILDER
![]()
![]()

**Bekannte Probleme & Lösungen:**

| Problem | Ursache | Lösung                                                                                                      |
|---------|---------|-------------------------------------------------------------------------------------------------------------|
| VM schien eingefroren bei "Loading essential drivers" | Unbeaufsichtigte Installation dauert länger als erwartet | VM ärgern, indem man auf "Aus" klickt und dann auf "Abbrechen". Dann fängt er plötzlich an weiter zu laden. |

### 5.3 Netzwerk

Nach Installation VM heruntergefahren mit `sudo poweroff`, danach Netzwerkadapter auf Internes Netzwerk - **labnet** umgestellt.

---

## 6. Windows 10 VM erstellen und installieren

### 6.1 VM-Erstellung in VirtualBox

| Einstellung | Wert |
|------------|------|
| VM-Name | Windows-Opfer |
| Ordner | C:\Users\LaraWurzbacher\Documents\VMs |
| ISO-Image | Windows 10 ISO (erstellt via MediaCreationTool_22H2) |
| Betriebssystem | Microsoft Windows |
| Version | Windows 10 (64-bit) |
| RAM | 4096 MB |
| CPUs | 2 |
| Festplatte | 50,00 GB (VDI, dynamisch alloziert) |
| Benutzername | opfer |
| Passwort | *(Gruppenpasswort, intern bekannt)* |
| Hostname | Windows-Opfer |

**Hinweis:** Windows 10 ISO wurde nicht direkt heruntergeladen, sondern über das MediaCreationTool erstellt.

**Netzwerk-Einstellung der VM:**
- Adapter 1: Internes Netzwerk - **labnet**
### 6.2 Installation

Unbeaufsichtigte Installation über VirtualBox automatisch durchgeführt.

**Installation erfolgreich abgeschlossen. **
 
---


## 7. IP-Konfiguration aller VMs

### 7.1 Kali Linux – 192.168.30.10

Konfiguration über `/etc/network/interfaces`:

```
auto eth0
iface eth0 inet static
    address 192.168.30.10
    netmask 255.255.255.0
    gateway 192.168.30.1
```

IP sofort gesetzt mit:
```
sudo ip addr add 192.168.30.10/24 dev eth0
```

Netzwerkadapter: `eth0`

**Bekannte Probleme & Lösungen:**

| Problem | Ursache | Lösung |
|---------|---------|--------|
| `systemctl: command not found` | Kali nutzt kein systemd in dieser Konfiguration | IP direkt mit `sudo ip addr add` gesetzt |
| Netzwerkadapter hieß `labnat` statt `labnet` | Tippfehler bei VM-Erstellung | In VirtualBox-Einstellungen korrigiert |

### 7.2 Ubuntu Suricata – 192.168.30.2

Konfiguration über /etc/netplan/50-cloud-init.yaml:

```
network:
    version: 2
    ethernets:
        enp0s3:
            addresses:
                - 192.168.30.2/24
            routes:
                - to: default
                  via: 192.168.30.1
```

Berechtigungen gesetzt und Konfiguration angewendet:
```
sudo chmod 600 /etc/netplan/50-cloud-init.yaml
sudo netplan apply
```
Netzwerkadapter: enp0s3 (bei Ubuntu Server anders als bei Kali)


### 7.3 Windows 10 – 192.168.30.20

Konfiguration über: Ausführen (`Win+R`) - `ncpa.cpl` - Rechtsklick Netzwerkadapter - Eigenschaften - IPv4:

| Einstellung | Wert |
|------------|------|
| IP-Adresse | 192.168.30.20 |
| Subnetzmaske | 255.255.255.0 |
| Standardgateway | 192.168.30.1 |

**Bekannte Probleme & Lösungen:**

| Problem | Ursache | Lösung |
|---------|---------|--------|
| VM war auf NAT statt Internes Netzwerk | Falsche Netzwerkeinstellung bei VM-Erstellung | In VirtualBox-Einstellungen auf Internes Netzwerk → labnet geändert |
 
---

## 8. Ping-Tests

Alle Tests durchgeführt von der **Kali-VM** aus.

| Von | Nach | IP | Ergebnis |
|-----|------|----|---------|
| Kali (192.168.30.10) | Ubuntu-Suricata | 192.168.30.2 | 0% packet loss |
| Kali (192.168.30.10) | Windows-Opfer | 192.168.30.20 | 0% packet loss |

Befehl:
```
ping -c 3 192.168.30.2
ping -c 3 192.168.30.20
```

**Bekannte Probleme & Lösungen:**

| Problem | Ursache | Lösung |
|---------|---------|--------|
| Ping zu Windows schlug fehl | Windows Firewall blockiert ICMP | Firewall-Regel hinzugefügt via CMD als Administrator: `netsh advfirewall firewall add rule name="ICMP" protocol=icmpv4:8,any dir=in action=allow` + Firewall deaktiviert |
| Ping zu Windows schlug weiterhin fehl | Netzwerkadapter-Name Tippfehler (labnat statt labnet) + Windows auf NAT | Beide Einstellungen in VirtualBox korrigiert |

# sind die noch richtig?
**Nachweis:** Screenshots `woche1_ping_kali_zu_ubuntu.png` und `woche1_ping_kali_zu_windows.png` 
 
---

## 9. Zusammenfassung – Finales Setup

Der ursprüngliche Plan sah drei VMs vor (Kali Linux, Windows 10, Ubuntu Server). Im Projektverlauf wurde das Setup schrittweise vereinfacht:

- **Windows 10 VM** wurde nicht verwendet, da VMs keinen echten WLAN-Adapter haben und sich daher nicht mit dem WiFi Pineapple verbinden können. Als Opfer-Gerät wurde stattdessen ein anderes Gerät eingesetzt und keine VM.
- **Ubuntu Server VM** wurde installiert und konfiguriert, im weiteren Projektverlauf jedoch nicht mehr aktiv genutzt, da Suricata nach Absprache mit der Dozentin (Mevre Tunca, 07.07.2026) nur theoretisch behandelt wird und kein Live-Nachweis erforderlich ist.

Das finale aktive Setup besteht damit aus **einer VM (Kali Linux)** als Angreifer-System, dem **WiFi Pineapple** als Rogue Access Point und einem **echten Endgerät** als Opfer-Gerät.

