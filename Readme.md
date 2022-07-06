# EPSLeskopf
![ ](https://github.com/esplesekopf/esplesekopf/blob/main/PIC/IMG_8185.jpg)

### _Die Idee_
Die Idee, welche hinter dem ESPLeskopf steckt, ist recht simpel erklärt. Ich wollte einen IR Lesekopf, um meinen Smart Meter Iskra MT681 auszulesen. Dabei war es mir wichtig, eine Platine zu haben, welche alles beinhaltet und außer eine Stromversorgung nichts weiter benötigt. Entstanden ist eine Platine mit integriertem ESP8285, USB-Chipsatz, IR Sende und IR Empfangselektrik und der Möglichkeit alle am Markt verfügbares Smart Meter auslesen zu können. Während die erste Version fertig gestellt war und durch ein Video auf Matthias Kleine Haus-Automatisierungs Youtube Kanal [`Haus-Automatisierung`](https://youtu.be/s6qQs4FN9B0) bekannt wurde, waren schon Ideen für Version 0.2 in Planung. Bei dieser Version ging es darum, nicht nur einen ESP auf der Platine verbaut zu haben, sondern die Platine selbst auch als USB Gerät nutzen zu könnnen. Und wenn USB schon einmal vorhanden ist, warum nicht auch gleich zum Programmieren des ESP nutzbar machen? Das Projekt war schon längst nicht mehr ein reines Bastler-Projekt, sondern es sollte nun Massentauglich werden.

### _Features_
Durch eine Jumperleiste kann die IR Sende und IR Empfangselektrik flexibel genutzt werden.
Entweder wird diese an zwei definierte GPIO's des ESP geführt oder an die Serielle Hardwareschnittstelle des ESP. Darüber hinaus kann der Lesekopf auch als USB Gerät genutzt werden, also kann die IR Sende und IR Empfangselektronik auch an den USB Chip geführt werden. Zu Test und Debug Zwecken kann auch eine Kreuzbeschaltung durchgeführt werden. So könnte die IR Sendeelektronik zum ESP geführt werden und die IR Empfangselektronik an den USP Chip. Einfach ausgedrückt: Der USB Chip ist nicht fest mit dem ESP Verbunden und auch nicht die IR Elektronik. Jede(r) kann entscheiden wie die Beschaltung erfolgen soll.

Durch diese Freiheit, können auch Smartmeter ausgelesen werden, die z.B. mit Initialisierungssequenzen arbeiten und während der Übertragung ihre Baudrate ändern, da diese zwingend die Serielle Hardwareschnittstelle benötigen.

Ebenso ist die Anbindung an einen Raspberry Pi oder PC denkbar (als USB Geräte).

Der USB Chip ermöglicht, neben dem Debug des ESP und den oben beschrieben Möglichkeiten, das einfache beschreiben des ESP mit jeder beliebigen Firmware.

Als Software kommt [`SMLReader`](https://github.com/mruettgers/SMLReader), [`ESPHome`](https://github.com/esphome), oder unserer Spezielle Version von [`Tasmota`](https://github.com/esplesekopf/Tasmota) zum Einsatz. 
Dabei werden die Daten per MQTT an einen einstellbaren MQTT Broker gesendet. So können sie von allen möglichen Systemen verarbeitet werden.

Eine weitere Funktion ist die Möglichkeit die PIN des eigenen Smart Meter an diesen zu senden. 
Dies wird mit meiner kleinen Änderung in SMLReader und einer extra Website möglich. Hier kann der eigene PIN-Code eingeben werden. Dieser wird dann an den Zähler gesendet.
Diese Funktion ersetzt die Taschenlampe welche bei den meisten Herstellern empfohlen um die PIN „einzugeben“. Das „Eingeben“ erfolgt durch Anblinken des Zählers mit der Taschenlampe. 
Um die PIN-Eingabe auch zu ermöglichen, wenn eine andere Software als SMLReader läuft, befindet sich im Deckel des Gehäuses eine Tastwippe, mit welcher ebenfalls die IR Sendediode angesteuert werden kann. So kann man den ESPLesekopf auf den eigenen Zähler setzen und direkt über die Tastwippe die PIN eingeben.

# Tasmota
## _Unterschiede_

Was unterscheidet nun "unsere" Version von Tasmota zu anderen?
Grundsätzlich muss man wissen, dass Tasmota nicht für das Auslesen von Smartmetern entwickelt wurde. Trotzdem wurde ein Modul für Tasmota entwickelt, welches die Verarbeitung solcher SML oder OBIS Daten ermöglicht. Dazu ist es aber nötig, Tasmota über ein Script diverse Einstellungen und Informationen mitzugeben. Genau in diesen Scripten liegt für viele das Problem und unser Lösungsansatz.

Das Erstellen der Scripte ist nicht ganz trivial, aber unerlässlich für den Betrieb. Also war die Idee geboren, funktionierende Scripte für die verschiedenen Zähler in Github zu pflegen. Über eine Auswahlfeld in der Scripting Webseite von Tasmota sind diese Scripte schnell zugänglich:
| Auswahlfeld | Scripte |
| ------ | ------ |
| ![ESPLesekopf](https://github.com/esplesekopf/esplesekopf/blob/main/PIC/IMG_9268.jpg) | ![ESPLesekopf](https://github.com/esplesekopf/esplesekopf/blob/main/PIC/IMG_9269.jpg) |



Wer Interesse an einem Lesekopf hat, kann mich gern unter esplesekopf@gmail.com kontaktieren und bitte anschließend unbedingt den eigenen SPAM Ordner kontrollieren!


# weitere Ideen
Nachdem der Lesekopf so gut läuft, steht nun die Weiterentwicklung an.

Was bedeutet das? Schon früh wurde ich mit dem Problem konfrontiert, das es bei einigen Installationen Probleme mit dem WLAN-Empfang gibt. 

Die Entwicklung geht nun dahin, dass es ein zusätzliches Gerät geben wird, welches auf einem ESP32 basiert und als wichtigstes Merkmal einen Netzwerkanschluss hat. Über diesen kann das Gerät per LAN-Patchkabel direkt mit dem Netzwerk verbunden werden. Da mir ein LAN Anschluss alleine nicht ausreichte, habe ich nach einer Platine gesucht die PoE unterstützt, also über das Netzwerkkabel auch mit Strom versorgt werden kann. 

Das Gerät besitzt ein Hutschienengehäuse (2TE) und wird wie eine Sicherung in die Unterverteilung eingebaut. Über 4 Anschlüsse können dann bis zu 4 Zähler oder beliebige andere Sensoren mit Tasmota ausgewertet werden.

Die Platine des ESPLesekopf ist schon für dieses Zusätzliche Gerät ausgelegt. Statt des ESP wird dann ein Connector verlötet. Der ESP auf dem ESPLesekopf wird nicht mehr benötigt, weil der ESP32 des Zusatzgerätes viel mehr Möglichkeiten bietet.

Hier ein Bild vom ersten Prototypen, noch ohne Externe WLAN Antenne.

![ESPLesekopf32](https://github.com/esplesekopf/esplesekopf/blob/main/PIC/ESPLesekopf32.JPG)

# Überschuss Laden mit evcc und dem Lesekopf
Ich hatte es vor kurzen auf Twitter geschrieben. Da ich einige Anfragen diesbezüglich bekommen habe, wollte ich hier einmal nur ganz kurz etwas zu diesem Thema schreiben. 

Ist eine PV Anlage vorhanden und man möchte das "Überschuss Laden" am E-Auto praktizieren, sind einige Dinge zu beachten:

> - es wird der aktuelle Überschuss der PV Anlage benötigt,<br>
> - eine Wallbox die die Stromstärke dynamisch anpassen kann und<br>
> - eine Software, welche die Steuerung des ganzen erledigt.<br>

Was ist nun dieses Überschussladen? E-Autos benötigen bei einer Ladung an der Wallbox mindestens eine Phase – also 230V – und mind. 6A Strom der fließen muss.<br>
Bei einigen wenigen kann der Strom auch weniger als 6A betragen, aber die Norm sagt „ab 6A kann es losgehen“. 

Mit dieser Information wird es ab jetzt alles sehr einfach: 230V * 6A = 1380W. Erzeugt also die PV Anlage Strom und bleiben nach Abzug des „Eigenverbrauch fürs Haus oder für was auch immer man den PV Strom nutzt“ noch die oben beschrieben 1380W über, dann speist man diese nicht ins Netz ein, sondern verwendet diesen Überschuss um damit besser den Akku des E-Autos zu laden. 

Da der PV Ertrag schwankt (Wolken, Wetter, Jahreszeit) aber der „Eigenverbrauch im Haus“ dabei gleich bleibt, muss es eine Instanz geben, welche hier immer gegensteuert, bzw. schaut ob denn noch genügend Überschuss da ist um wirklich das E-Auto laden zu können.<br>
Ist dieser nicht da und die Wallbox lädt weiter, wird unweigerlich Strom aus dem Netz bezogen und das bedeutet: Es kostet Geld statt Geld zu sparen.

Die Software welche dies erledigt ist bei mir [`evcc`](https://evcc.io). Es ist ein Open Source Projekt, mit dem man sich anfreunden muss. Die Doku ist aus meiner Sicht zwischen 3 und 3- anzusiedeln. Auch ist das auf Github vorandene Diskussionsforum „ok“ aber auch nicht mehr. Schlussendlich funktioniert es aber und da kann man dann nicht meckern. Es ist wie so viele typische OpenSource Projekt – aber lassen wir das.

Nun braucht man bei evcc eine Komponente die dem System sagt was denn aktuelle so los ist im eigenen Stromkasten. Diese Komponente nennt sich „grid“.<br>
Ein "grid" ist nichts anderes als ein Gerät was Informationen zum aktuellen Stromverbrauch und der aktuellen Einspeisung liefert. <br>
Bei vielen PV Anlagen ist ein solcher Stromzähler entweder schon enthalten oder wurde zusätzlich eingebaut. Entweder sitzt dieser direkte hinter dem Netzstromzähler und wurde zwischen Netzstromzähler und Sicherungen „eingeschleift“. Oder er sitzt irgendwo im Zählerkasten und verfügt über Strommessklemmen. Dies sind kleine Spulen wo die Kabel vom Netzstromzähler pro Phase durchgeführt werden oder welch um die Kabel gelegt werden. So können diese den Strom messen der durch die Adern fließt und zwar „Rein“ wie „Raus“.

Hat man so etwas nicht, oder eine Balkonanlage oder eine Selbst gebaute PV Anlage oder einfach so einen ESPLesekopf, kann man auch am Netzstromzähler diese Daten abgreifen. Viele dieser Zähler sind „zwei-Richtungs-Zähler“. Sie erfassen also „Rein“ wie „Raus“ und am Netzstromzähler wird schließlich gezählt was man bezahlt.

Als "grid" kann also auch der Lesekopf dienen, wenn er z.B. mit Tasmota läuft. 
Tasmota kann über eine http api die Werte des Stromzählers als json ausgeben. Der Aufruf erfolgt mit `http://ipadressedeslesekopf/cm?cmnd=status%208`. <br>
Abhängig vom verwendeten Script, erhält man hier unterschiedliche Bezeichnungen.

Ein Beispiel:
Ein typischen Script für den ESPLesekopf sieht so aus:
```
>D
;*****
; for / fuer ISKRA MT691
;*****
;After selecting a script, be sure to activate it and reboot!
;Nachdem auswaehlen eines Scripts, unbedingt dieses aktivieren und einen Neustart durchfuehren!
>B
=>sensor53 r
;Interval fuer das Senden von Daten auf 10Sekunden / Set teleperiod to 10sec  
tper=3
>M 1
+1,14,s,0,9600,Strom
1,77070100010800ff@1000,Gesamt Verbrauch,KWh,Total_in,3
1,77070100100700ff@1,Aktueller Verbrauch,W,Power_cur,0
1,77070100020800ff@1000,Gesamt Netzeinspeisung,KWh,Total_out,3
1,77070100000009ff@#,Zaehler ID,,Meter_id,0|
#
```

Die Ausgabe von `.../cm?cmnd=status%208` sieht so aus:<br>
`{"StatusSNS":{"Time":"2022-07-06T21:00:25","Strom":{"Total_in":9982.810,"Power_cur":7,"Total_out":9055.830,"Meter_id":"090"}}}`

Zu erkennen ist der Aufbau des JSON Datagram. `1,77070100010800ff@1000,Gesamt Verbrauch,KWh,Total_in,3` im Script erzeugt eine Variabel "Total_in". Diese findet sich auch im JSON wieder. Auch "Power_cur" aus der Script Zeile `1,77070100100700ff@1,Aktueller Verbrauch,W,Power_cur,0` findet sich wieder. Da jeder sein Script vielleicht anders schreibt und auch andere Variablen nutzt ist das Ganze für EVCC nicht nach Schema F zu bewältigen.

Schauen wir uns die evcc.yaml Konfigurationsdatei an, zumindest die hier relevanten Abschnitte:
```
.
.
.
meters:
- type: custom
  name: lesekopf
  power:
   source: http
   uri: http://192.168.178.77/cm?cmnd=status%208
   jq: .StatusSNS.Strom.Power_cur
.
.
.
site:
  title: Home
  meters:
    grid: lesekopf
.
.
.
```

Es werden mit "meters:" die ganzen Stromzähler definiert. Hier kommt nun die http Abfrage von Tastmota zum Tragen. In diesem Beispiel hat der Lesekopf die IP 192.168.178.77.<br>
Wichtig ist aber die Zeile `jq`. jq wird zum filtern von Daten aus einem JSON Datagram genutzt. Hier kommen nun wieder die oben beschrieben Variablen des Tasmota script ins Spiel. Das jq filtert auf `.StatusSNS.Strom.Power_cur` und dies findet sich im JSON Datagram: {"`StatusSNS`":{"Time":"2022-07-06T21:00:25","`Strom`":{"Total_in":9982.810,"`Power_cur`":7,"Total_out":9055.830,"Meter_id":"090"}}} <br>

Ein JSON muss man sich wie ein Baum vorstellen. Oben steht `StatusSNS`. Darunter `Time` und `Strom`. <br>
```
|-StatusSNS
  |-Time
  |-Strom
    |-Power_cur
    |-Total_out
    |-Meter_id
```
Time hat hier einfach den Wert 2022-07-06T21:00:25. Strom hat aber wiederrum eine weitere Verzweigung `Power_cur`, `Total_out` und `Meter_id`. Um nun in diesem JSON Baum einen Punkt anzusteuern geht man den Baum von oben nach untern ab und zweigt immer mit einem PUNKT ab bis man "angekommen" ist. 


Benennt man nun im Script die Variablen anders,

statt `+1,14,s,0,9600,Strom` z.B. `+1,14,s,0,9600,AUA`
und statt `1,77070100010800ff@1000,Gesamt Verbrauch,KWh,Total_in,3` z.B. `1,77070100010800ff@1000,Gesamt Verbrauch,KWh,REIN,3`
und statt `1,77070100100700ff@1,Aktueller Verbrauch,W,Power_cur,0` z.B. `1,77070100100700ff@1,Aktueller Verbrauch,W,RAUS,0`

müsste das jq statt `.StatusSNS.Strom.Power_cur` dann `.StatusSNS.AUS.RAUS` lauten.

Ich hoffe das war einigermaßen verständlich.
