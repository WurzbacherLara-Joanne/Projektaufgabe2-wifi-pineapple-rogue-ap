# Suricata-Regelwerk: Erläuterung der Regeln

## Wie eine Suricata-Regel aufgebaut ist

Eine Regel besteht immer aus drei Teilen. Zuerst die Aktion, bei uns durchgängig `alert`, weil Suricata als IDS nur melden und nicht blockieren soll. Danach der Header, also das Protokoll (zum Beispiel `http` oder `dns`), Quelle, Ziel und die Richtung des Verkehrs. Zum Schluss die Optionen in Klammern, und darin steckt das eigentliche Erkennungsmuster.

In den Optionen legt ein sogenannter Sticky Buffer fest, welchen Teil eines Pakets sich die Regel überhaupt anschaut, etwa `http.host` für das Host-Feld oder `dns.query` für eine DNS-Anfrage. Das `content` dahinter ist der Text oder die Bytefolge, nach der in diesem Bereich gesucht wird. Dazu kommen Angaben wie `flow` für den Verbindungszustand, eine eindeutige ID (`sid`, bei eigenen Regeln immer ab 1000000) und eine Kategorie (`classtype`).

Man kann eine Regel also wie einen Satz lesen: "Melde HTTP-Verkehr Richtung Server, wenn im Host-Feld neverssl.com steht." Nach diesem Prinzip sind alle folgenden Regeln gebaut.

## Die sechs Regeln

Die Regeln decken beide in der Aufgabe geforderten Angriffsvektoren ab: den externen Rogue Access Point (R1 bis R3) und das interne ARP-Spoofing mitsamt seinen Folgen (R4 bis R6).

### R1: Klartext-HTTP-Zugriff (SID 1000001)
#     (genau der Aufruf aus dem Pineapple-Ablaufplan)
alert http any any -> any any (msg:"LAB Rogue-AP: Klartext-HTTP-Zugriff auf neverssl.com"; flow:established,to_server; http.host; content:"neverssl.com"; sid:1000001; rev:1; classtype:policy-violation;)

Diese Regel schaut sich bei HTTP-Verbindungen das Host-Feld an und schlägt an, wenn dort neverssl.com auftaucht. Sobald das Opfer diese Seite über den Rogue AP aufruft, wird ein Alert geschrieben. Wir nutzen neverssl.com bewusst, weil die Seite garantiert nur über HTTP läuft und damit lesbaren Klartext erzeugt. Die Regel greift ausschließlich bei unverschlüsseltem Verkehr, was die Kernaussage stützt: Über den Rogue AP ist alles mitlesbar, was nicht verschlüsselt ist. Angriffsvektor: Rogue Access Point.

### R2: Zugangsdaten im Klartext (SID 1000002)
#     Feuert, wenn ein Login-Formular unverschluesselt abgeschickt wird.
alert http any any -> any any (msg:"LAB Rogue-AP: Moegliche Klartext-Credentials in HTTP-POST"; flow:established,to_server; http.method; content:"POST"; http.request_body; content:"password"; nocase; sid:1000002; rev:1; classtype:policy-violation;)

Hier prüft die Regel zwei Dinge gleichzeitig. Die HTTP-Methode muss `POST` sein, und im Body der Anfrage muss das Wort "password" vorkommen. Trifft beides zu, feuert die Regel. Das passiert typischerweise, wenn ein Login-Formular unverschlüsselt abgeschickt wird, sodass der Angreifer die Zugangsdaten direkt mitlesen könnte. Die Regel matcht die Feldbezeichnung "password"; andere Schreibweisen wie "pwd" bräuchten eine eigene Regel. Angriffsvektor: Rogue Access Point.

### R3: HTTP Basic Auth im Klartext (SID 1000003)
#     Base64 ist KEINE Verschluesselung – triviale Entschluesselung moeglich.
alert http any any -> any any (msg:"LAB Rogue-AP: HTTP Basic Auth im Klartext"; flow:established,to_server; http.header; content:"Authorization|3a| Basic"; nocase; sid:1000003; rev:1; classtype:policy-violation;)

Diese Regel durchsucht die HTTP-Header nach "Authorization: Basic" und erkennt damit HTTP Basic Authentication. Bei diesem Verfahren werden Benutzername und Passwort nur mit Base64 kodiert übertragen. Base64 ist aber keine Verschlüsselung, sondern lässt sich in einem einzigen Schritt wieder in Klartext zurückrechnen. Über einen Rogue AP sind solche Zugangsdaten also praktisch offen. Angriffsvektor: Rogue Access Point.

### R4: ARP-Sichtbarkeit (SID 1000010)
#     Hinweis: feuert auf ALLE ARP-Pakete -> bewusst "laut".
#     Ideal zum Offline-Test gegen die Angriffs-PCAP, wo man die
#     Flut an gefaelschten ARP-Replies des Angreifers SEHEN will.
alert ip any any -> any any (msg:"LAB ARP-Frame erkannt (Sichtbarkeit)"; decode-event:ethernet.unknown_ethertype; sid:1000010; rev:1; classtype:not-suspicious;)

Diese Regel soll ARP-Aktivität im Testnetz sichtbar machen, denn beim ARP-Spoofing überflutet der Angreifer das Netz mit gefälschten ARP-Antworten, um sich als Gateway auszugeben. Wichtig zur Einordnung: Suricata arbeitet auf IP-Ebene, ARP läuft aber eine Schicht darunter auf Layer 2. Selbst mit dem ARP-Decoder aus Suricata 8.0 ist die regelbasierte ARP-Erkennung in der Praxis begrenzt und eher zur Protokollierung geeignet. Deshalb verstehen wir R4 als Analysehilfe und übergeben die eigentliche ARP-Erkennung an arpwatch, das genau für solche Layer-2-Anomalien gebaut ist. Angriffsvektor: ARP-Spoofing.

### R5: DNS-Anfrage sichtbar (SID 1000011)
#     Bei erfolgreichem MITM laeuft auch die DNS-Aufloesung ueber den
#     Angreifer. Diese Regel zeigt, dass DNS im Klartext mitlesbar ist.
#     -> "example.com" durch eure konkrete Testdomain ersetzen.
alert dns any any -> any any (msg:"LAB MITM: DNS-Anfrage auf Testdomain sichtbar"; dns.query; content:"example.com"; nocase; sid:1000011; rev:1; classtype:not-suspicious;)

Die Regel durchsucht DNS-Anfragen nach einer festgelegten Zieldomain und schlägt an, sobald diese im Netz auftaucht. Nach einem erfolgreichen ARP-Spoofing läuft auch die Namensauflösung des Opfers über den Angreifer, sodass dessen DNS-Anfragen für ihn mitlesbar werden. Die Domain ist im Skript ein Platzhalter und wird im Labor auf die tatsächlich verwendete Testdomain gesetzt. Angriffsvektor: Folge des ARP-Spoofings.

### R6: DNS-Antwort zeigt auf die Angreifer-IP (SID 1000012)
#     Wenn eine DNS-Antwort die Kali-IP (192.168.30.10) enthaelt, wurde
#     die Namensaufloesung manipuliert. rdata-Match ueber content.
alert dns any any -> any any (msg:"LAB DNS-Spoofing: Antwort zeigt auf Angreifer-IP 192.168.30.10"; flow:to_client; content:"|c0 a8 1e 0a|"; sid:1000012; rev:1; classtype:bad-unknown;)
# Hinweis zu R6: |c0 a8 1e 0a| = 192.168.30.10 in Hex (die 4 IP-Bytes).

Diese Regel sucht in DNS-Antworten nach den vier Bytes der Angreifer-IP, hinterlegt als Hexwert von 192.168.30.10. Findet sie eine solche Antwort, ist das ein starkes Indiz dafür, dass die Namensauflösung manipuliert wurde und das Opfer auf den Rechner des Angreifers umgeleitet werden soll. Von allen Regeln ist das der direkteste Angriffsnachweis, weil eine normale DNS-Antwort nie auf die Angreifer-IP zeigt. Die IP muss im Labor an die tatsächliche Adresse angepasst werden. Angriffsvektor: DNS-Spoofing als Folge des ARP-Spoofings.

## Einordnung

Kein einzelnes Werkzeug erkennt beide Angriffe vollständig. Suricata deckt die Folgen eines Angriffs auf IP- und Anwendungsebene ab, also Klartext-Verkehr, abgegriffene Zugangsdaten und manipulierte DNS-Antworten. Die Ursache des internen Angriffs, der plötzliche Wechsel einer MAC-Adresse zu einer bekannten IP, wird zuverlässig von arpwatch erkannt. Erst zusammen decken die beiden Werkzeuge Rogue AP und ARP-Spoofing ab. Dass die HTTP-Regeln nur bei unverschlüsseltem Verkehr greifen, ist dabei kein Mangel, sondern zeigt umgekehrt, wie gut TLS schützt.
