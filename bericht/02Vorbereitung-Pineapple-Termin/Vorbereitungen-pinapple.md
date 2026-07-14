
# Woche 2 – Vorbereitung Pineapple-Termin

---

## 1. Wireshark & arpspoof – Installation auf Kali

Vor der Installation musste der Netzwerkadapter der Kali-VM temporär auf **NAT** umgestellt werden, da die VM im internen Netzwerk (labnet) keinen Internetzugang hat.

**Vorgang:**
- VirtualBox - Kali-Angreifer - Ändern - Netzwerk - NAT
- VM neu starten
- DHCP-IP automatisch holen:

```
sudo ip addr flush dev eth0
sudo ip link set eth0 down
sudo ip link set eth0 up
sudo dhcpcd eth0
```

- Danach `sudo apt update` und Installation:

```
sudo apt install wireshark -y
sudo apt install dsniff -y
```

**Ergebnis:** Beide Tools waren bereits in Kali vorinstalliert (Kali bringt Sicherheitstools standardmäßig mit).

Nach der Installation Netzwerkadapter wieder zurück auf **Internes Netzwerk - labnet** gestellt.

![wireshark gestartet](https://github.com/WurzbacherLara-Joanne/Projektaufgabe2-wifi-pineapple-rogue-ap/blob/main/screenshots/02Vorbereitung-Pineapple-Termin/wireshark_starten.png)

---

## 2. IP-Forwarding aktivieren (Kali)

IP-Forwarding ist notwendig für den ARP-Spoofing-Angriff, damit Kali den Traffic des Opfers weiterleiten kann.

```
echo 1 | sudo tee /proc/sys/net/ipv4/ip_forward
cat /proc/sys/net/ipv4/ip_forward
```

Ausgabe: `1` = IP-Forwarding aktiv 

![ipforward](https://github.com/WurzbacherLara-Joanne/Projektaufgabe2-wifi-pineapple-rogue-ap/blob/main/screenshots/02Vorbereitung-Pineapple-Termin/ipforward_aktivieren.png)

**Hinweis:** Diese Einstellung ist temporär und muss nach jedem Neustart neu gesetzt werden. Für den Pineapple-Termin direkt zu Beginn ausführen.

---

## 3. IP-Adressen dauerhaft konfigurieren

### 3.1 Kali Linux – 192.168.30.10

Konfiguration bereits in `/etc/network/interfaces` vorhanden (aus Woche 1).
Nach Neustart getestet: IP 192.168.30.10 automatisch gesetzt.

![neustartkali](https://github.com/WurzbacherLara-Joanne/Projektaufgabe2-wifi-pineapple-rogue-ap/blob/main/screenshots/02Vorbereitung-Pineapple-Termin/kali_angreifer_reboot.png)

### 3.2 Ubuntu Suricata – 192.168.30.2

Wie bereits erwähnt nutzt Ubuntu **Netplan** statt `/etc/network/interfaces`.
Konfiguration in `/etc/netplan/50-cloud-init.yaml`:

```yaml
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

Berechtigungen gesetzt und angewendet:

```
sudo chmod 600 /etc/netplan/50-cloud-init.yaml
sudo netplan apply
```

Nach Neustart getestet: IP 192.168.30.2 automatisch gesetzt

![dauerhafteip](https://github.com/WurzbacherLara-Joanne/Projektaufgabe2-wifi-pineapple-rogue-ap/blob/main/screenshots/02Vorbereitung-Pineapple-Termin/ubuntu_suricata_reboot.png)

---

## 4. Problem: Windows-VM hat keinen WLAN-Adapter

### Problem
Die Windows-Opfer-VM hat keinen WLAN-Adapter. VMs haben standardmäßig nur einen virtuellen Ethernet-Adapter, keinen echten WLAN-Chip. Eine Verbindung mit dem WiFi Pineapple per WLAN ist daher nicht möglich.

### Lösung
Statt der Windows-VM wird beim Pineapple-Termin ein **echtes Endgerät** als Opfer-Gerät verwendet. Das ist technisch gleichwertig und sogar realistischer für die Demonstration.

### Vorbereitung für den Termin
Das Opfer-Endgerät muss sich **vor dem Termin einmal mit der Test-SSID verbunden haben**, damit das Endgerät sie als bekanntes Netz speichert und sich beim Termin automatisch verbindet:

1. Eine Person macht einen Handy-Hotspot mit dem Namen `HTW-Guest` auf
2. Das Opfer-Endgerät verbindet sich einmal damit
3. Hotspot wieder abschalten
4. Beim Pineapple-Termin sendet der Pineapple `HTW-Guest` aus = Opfer-Endgerät verbindet sich automatisch


Handy-Hotspot muss das gleiche Passwort wie der Pineapple in dem Falle zum testen verwenden.

![handy](https://github.com/WurzbacherLara-Joanne/Projektaufgabe2-wifi-pineapple-rogue-ap/blob/main/screenshots/02Vorbereitung-Pineapple-Termin/opfer_handyhotspot.png)


---

