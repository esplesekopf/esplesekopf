# EPSLeskopf
![ ](https://github.com/esplesekopf/esplesekopf/blob/main/PIC/IMG_8185.jpg)

### _Die Idee_
Die Idee, welche hinter dem ESPLeskopf steckt, ist recht simpel erklärt. Ich brauchte einen IR Lesekopf, um meinen Smart Meter Iskra MT681 auszulesen. Dabei war es mir wichtig, eine Platine zu haben, welche alles beinhaltet und außer eine Stromversorgung nichts weiter benötigt. Entstanden ist eine Platine mit integriertem ESP8285, USB-Chipsatz, Sende und Empfangselektrik und der Möglichkeit alle am Markt verfügbares Smart Meter auslesen zu können.

### _Features_
Durch eine Jumperleiste kann die Sende und Empfangselektrik flexibel genutzt werden.
Entweder wird diese an zwei definierte GPIO's des ESP geführt oder an die Serielle Hardwareschnittstelle des ESP geführt. Darüber hinaus kann der Lesekopf auch als USB Gerät genutzt werden, also kann die Sende und Empfangselektronik auch an den USB Chip geführt werden. Zu Test und Debug Zwecken kann auch eine Kreuzbeschaltung durchgeführt werden. So könnte die Sendeelektronik zum ESP geführt werden und die Empfangselektronik an den USP Chip.

Durch diese Freiheit, können auch Smartmeter ausgelesen werden, die z.B. mit Initialisierungssequenzen arbeiten und während der Übertragung ihre Baudrate ändern, da diese zwingend die Serielle Hardwareschnittstelle benötigen.

Ebenso ist die Anbindung an einen Raspberry Pi oder PC denkbar (als USB Geräte).

Der USB Chip ermöglicht, neben dem Debug des ESP und den oben beschrieben Möglichkeiten, das einfache beschreiben des ESP mit jeder beliebigen Firmware.

Als Software kommt [`SMLReader`](https://github.com/mruettgers/SMLReader), [`ESPHome`](https://github.com/esphome), oder unserer Spezielle Version von [`Tasmota`](https://github.com/esplesekopf/Tasmota) zum Einsatz. 

# Tasmota
## _Unterschiede_

Was unterscheidet nun "unserer" Version von Tasmota zu anderen?
Grundsätzlich muss man wissen, dass Tasmota nicht für das Auslesen von Smartmetern entwickelt wurde. Trotzdem wurde ein Modul für Tasmota entwickelt, welches die Verarbeitung der Daten 

Das Erstellen der Scripte ist nicht ganz trivial, aber unerlässlich für den Betrieb. Also war die Idee geboren, funktionierende Scripte für die verschiedenen Zähler in Github zu pflegen. Über eine Auswahlfeld in der Scripting Webseite von Tasmota sind diese Scripte schnell zugänglich:
| Auswahlfeld | Scripte |
| ------ | ------ |
| ![ESPLesekopf](https://github.com/esplesekopf/esplesekopf/blob/main/PIC/IMG_9268.jpg) | ![ESPLesekopf](https://github.com/esplesekopf/esplesekopf/blob/main/PIC/IMG_9269.jpg) |
