# ESPLeskopf
![ ](https://github.com/esplesekopf/esplesekopf/blob/main/PIC/IMG_8185.jpg)

### Kontakt
[per Mail](mailto:esplesekopf@gmail.com)\
[auf Twitter](https://twitter.com/th3_s4int)

### _Die Idee_
Die Idee, welche hinter dem ESPLeskopf steckt, ist recht simpel erklärt. Ich wollte einen IR Lesekopf, um meinen Smart Meter Iskra MT681 auszulesen. Dabei war es mir wichtig, eine Platine zu haben, welche alles beinhaltet und außer eine Stromversorgung nichts weiter benötigt. Entstanden ist eine Platine mit integriertem ESP8285, USB-Chipsatz, IR Sende und IR Empfangselektronik und der Möglichkeit alle am Markt verfügbares Smart Meter auslesen zu können. Während die erste Version fertig gestellt war und durch ein Video auf Matthias Kleine Haus-Automatisierungs Youtube Kanal [`Haus-Automatisierung`](https://youtu.be/s6qQs4FN9B0) bekannt wurde, waren schon Ideen für Version 0.2 in Planung. Bei dieser Version ging es darum, nicht nur einen ESP auf der Platine verbaut zu haben, sondern die Platine selbst auch als USB Gerät nutzen zu können. Und wenn USB schon einmal vorhanden ist, warum nicht auch gleich zum Programmieren des ESP nutzbar machen? Das Projekt war schon längst nicht mehr ein reines Bastler-Projekt, sondern es sollte nun Massentauglich werden.

### _Features_
Durch eine Jumperleiste kann die IR Sende und IR Empfangselektrik flexibel genutzt werden.
Entweder wird diese an zwei definierte GPIO's des ESP geführt oder an die Serielle Hardwareschnittstelle des ESP. Darüber hinaus kann der Lesekopf auch als USB Gerät genutzt werden, also kann die IR Sende und IR Empfangselektronik auch an den USB Chip geführt werden. Zu Test und Debug Zwecken kann auch eine Kreuzbeschaltung durchgeführt werden. So könnte die IR Sendeelektronik zum ESP geführt werden und die IR Empfangselektronik an den USP Chip oder umgekehrt. Einfach ausgedrückt: Der USB Chip ist nicht fest mit dem ESP Verbunden und auch nicht die IR Elektronik. Jede(r) kann entscheiden, wie die Beschaltung erfolgen soll.

Durch diese Freiheit, können auch Smartmeter ausgelesen werden, die z.B. mit Initialisierungssequenzen arbeiten und während der Übertragung ihre Baudrate ändern, da diese zwingend die Serielle Hardwareschnittstelle benötigen.

Ebenso ist die Anbindung an einen Raspberry Pi oder PC denkbar (als USB Geräte).

Der USB Chip ermöglicht, neben dem Debug des ESP und die oben beschrieben Möglichkeiten, das einfachen beschreiben des ESP mit jeder beliebigen Firmware.

Als Software kommt [`SMLReader`](https://github.com/mruettgers/SMLReader), [`ESPHome`](https://github.com/esphome), oder unserer Spezielle Version von [`Tasmota`](https://github.com/esplesekopf/Tasmota) zum Einsatz. 
Dabei werden die Daten per MQTT an einen einstellbaren MQTT Broker gesendet. So können sie von allen möglichen Systemen verarbeitet werden.

Eine weitere Funktion ist die Möglichkeit die PIN des eigenen Smart Meter an diesen zu senden. 
Dies wird mit meiner kleinen Änderung in SMLReader und einer extra Website möglich. Hier kann der eigene PIN-Code eingeben werden. Dieser wird dann an den Zähler gesendet.
Diese Funktion ersetzt die Taschenlampe welche bei den meisten Herstellern empfohlen um die PIN „einzugeben“. Das „Eingeben“ erfolgt durch Anblinken des Zählers mit der Taschenlampe. 
Um die PIN-Eingabe auch zu ermöglichen, wenn eine andere Software als SMLReader läuft, befindet sich im Deckel des Gehäuses eine Tastwippe, mit welcher ebenfalls die IR Sendediode angesteuert werden kann. So kann man den ESPLesekopf auf den eigenen Zähler setzen und direkt über die Tastwippe die PIN eingeben.

Die Arbeit am Projekt geht weiter und im Sep 2022 arbeite ich an Version 0.4 der Platine. Durch den Bauteilmangel war ich gezwungen die verwendeten Taster durch andere Typen zu ersetzen. Da diese etwas mehr Platz einnehmen als die Alten, habe ich direkt das Layout der Platine angepasst. Dabei wurden auch einige Änderungen für die Nutzung von Akkus als Spannungsversorgung eingebracht. Der ESP kann nun in den DeepSleep versetzt werden und auch die USB-Chip kann komplett vom Strom genommen werden.\
![ ](https://github.com/esplesekopf/esplesekopf/blob/main/PIC/Version0.4.jpg)

Außerdem habe ich mich um das Thema Gehäuse gekümmert. Dieses lasse ich nun im [SLS Druckverfahren](https://www.3dbavaria.com/3d-druck-technologien/sls-3d-druck/) fertigen. Die Qualität ist etwas ganz anderes:\
![ ](https://github.com/esplesekopf/esplesekopf/blob/main/PIC/NewCase.jpg)

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
Ich hatte es vor kurzen auf Twitter geschrieben. Da ich einige Anfragen diesbezüglich bekommen habe, wollte ich hier einmal nur ganz kurz etwas zu diesem Thema schreiben: [`Zähler fürs Überschussladen`](https://github.com/esplesekopf/esplesekopf/blob/main/ueberschussladen.md)
