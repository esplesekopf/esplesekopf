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

statt `+1,14,s,0,9600,Strom` z.B. `+1,14,s,0,9600,AUA` <br>
und statt `1,77070100010800ff@1000,Gesamt Verbrauch,KWh,Total_in,3` z.B. `1,77070100010800ff@1000,Gesamt Verbrauch,KWh,REIN,3` <br>
und statt `1,77070100100700ff@1,Aktueller Verbrauch,W,Power_cur,0` z.B. `1,77070100100700ff@1,Aktueller Verbrauch,W,REINuRAUS,0` <br>

müsste "jq" statt `.StatusSNS.Strom.Power_cur` dann `.StatusSNS.AUA.REINuRAUS` lauten.

Ich hoffe das war einigermaßen verständlich.
