# EPSLeskopf

### _Die Idee_
Die Idee welche hinter dem ESPLeskopf steckt, ist recht simpel erklärt. Ich brauchte einen IR Lesekopf um meinen SmartMeter Iskra MT681 auszulesen. Dabei war es mir wichtig, eine Platine zu haben, welche alles behinhaltet und außer eine Stromversorgung nichts weiter benötigt. Entstanden ist eine Platine mit integrietem ESP8285, USB Chipsatz, Sende und Empfangselektrik und der Möglichkeit alle am Markt verfügbares SmartMeter auslesen zu können.

### _Features_
Durch eine Jumperleiste kann die Sende und Empfangselektrik flexibel genutzt werden.
Entweder wird diese an zwei definierte GPIO's des ESP geführt oder an die Serielle Hardwareschnittstelle des ESP geführt. Darüberhinhaus kann der Lesekopf auch als USB Gerät genutzt werden, also kann die Sende und Empfangselektronik auch an den USB Chip geführt werden. Zu Test und Debug Zwecken kann auch eine Kreuzbeschaltung durchgeführt werden. So könnte die Sendeelektronik zum ESP geführt werden und die Empfangselektronik an den USP Chip.

Durch diese Freiheit, können auch Smartmeter ausgelesen werden, die z.B. mit Initialisierungssequenzen arbeiten und wärend der Übertragung ihre Baudrate ändern, da diese zwingend die Serielle Hardwarschnittstelle benötigen.

Ebenso ist die Anbindung an einen Rasperry Pi oder PC denkbar (als USB Geräte).

Der USB Chip ermöglicht, neben dem Debug des ESP und den oben beschrieben Möglichkeiten, das einfache beschreiben des ESP mit jeder beliebigen Firmware.

Als Software kommt [`SMLReader`](https://github.com/mruettgers/SMLReader), [`ESPHome`](https://github.com/esphome), oder unserer Spezielle Version von [`Tasmota`](https://github.com/esplesekopf/Tasmota) zum Einsatz. 

# Tasmota
## _Unterschiede_

Was unterscheidet nun "unserer" Version von Tasmota zu anderen?
Grundstätzlich muss man wissen, das Tasmota nicht für das Auslesen von Smartmetern entwickelt wurde. Trotzdem wurde ein Modul für Tasmota entwickelt, welches die Verarbeitung der Daten ermöglicht. Dazu ist es aber nötig, Tasmota über ein Script diverse Einstellungen und Informationen mitzugeben. Genau hier liegt aber das Problem und unser Lösungsansatz.

Das erstellen der Script ist nicht ganz trivial aber unerlässlich für den Betrieb. Also war die Idee geboren, funktionierende Scripte für die verschiedenen Zähler in Github zu pflegen. Über eine Auswahlfeld in der Scripting Webseite von Tasmota sind diese Script schnell zugänglich:
| ------ | ------ |
| [![ESPLesekopf](https://github.com/esplesekopf/esplesekopf/blob/main/PIC/IMG_9268.jpg)] | [![ESPLesekopf](https://github.com/esplesekopf/esplesekopf/blob/main/PIC/IMG_9269.jpg)] |
