# Überschuss Laden mit evcc und dem Lesekopf
Ich hatte es vor kurzen auf Twitter geschrieben, dass ich den Lesekopf in Verbindung mit evcc zum Überschussladen nutze. Da ich einige Anfragen diesbezüglich bekommen habe, wollte ich hier einmal - nur ganz *kurz* :) - etwas zu diesem Thema schreiben. 

### Technik
Ist eine PV Anlage vorhanden und man möchte das "Überschuss-Laden" am E-Auto praktizieren, sind einige Dinge zu beachten:

> - es braucht grundsätzlich eine PV Anlage die genügend Energie erzeugt um Überschussladen zu ermöglichen (gleich mehr),<br>
> - eine Wallbox die die Stromstärke dynamisch anpassen kann und<br>
> - eine Software, welche die Steuerung des ganzen erledigt. <br>
> - ein Gerät, welches der Steuerung sagt was denn so los ist im Stromkasten in Bezug auf "Erzeugen" wir gerade Strom oder "Beziehen" wir gerade Strom aus dem Netz. <br>

Was ist nun dieses Überschussladen und warum geht dies nicht mit jeder PV Anlage? E-Autos benötigen bei einer Ladung an der Wallbox mindestens eine Phase – also 230V (z.B. Schuko Steckdose) – und es muss mind. ein Strom von 6A fließen. <br>
Bei einigen wenigen kann der Strom auch weniger als 6A betragen, bei einigen anderen - wie frühere Modelle der Zoe auch mal mind. 8A, aber die Norm sagt „ab 6A kann es losgehen“. 

Mit dieser technischen Information wird es ab jetzt alles sehr einfach, denn: 230V * 6A = 1380W. Erzeugt also die PV Anlage Strom und bleiben nach Abzug des „Eigenverbrauch fürs Haus oder für was auch immer man den PV Strom nutzt“ noch die oben beschrieben 1380W über, hat man einen Überschuss. Gewöhnlich wird ist der Überschuss der Teil der erzeugten Energie den man nicht selbst verbraucht und welcher dann ins Netz eingespeist wird. Dass die aktuelle Vergütung für diese Einspeisung ehr symbolischen Charakter hat, ist jedem PV Besitzer klar. <br>
Es ist also weitaus sinnvoller diesen Überschuss in das E-Auto zu laden, statt zu verschenken. Auch erhöht dies den Eigenanteil der PV Nutzung und hilft dabei das sich die Anlage schneller rechnet. 

Jeder weiß: der PV Ertrag schwankt (Wolken, Wetter, Jahreszeit), der „Eigenverbrauch im Haus“ ändert sich eventuell auch von Jahreszeit zu Jahreszeit. Es muss eine Instanz geben, welche hier immer gegensteuert, bzw. schaut, ob denn noch genügend Überschuss da ist, um wirklich das E-Auto laden zu können. <br>
Wäre - z.B. durch Wolken - kein ausreichender Überschuss da oder ein eventuell vorhandener Stromspeicher muss erste gefüllt werden, dann wäre es äußerst ungünstig, wenn die Wallbox einfach weiter das Auto lädt, denn dadurch würde Strom aus dem Netz bezogen was Geld kostet, statt Geld zu sparen.

Die Software welche dies erledigt ist bei mir [`evcc`](https://evcc.io). Es ist ein Open Source Projekt, mit dem man sich anfreunden muss. Die Doku ist aus meiner Sicht zwischen 3 und 3- anzusiedeln. Auch ist das auf GitHub vorhandene Diskussionsforum „ok“ aber auch nicht mehr. Schlussendlich funktioniert es aber und da kann man dann nicht meckern. Es ist wie so viele typische Open Source Projekt – aber lassen wir das.

Ziel ist es, das evcc ständig überprüft wie viel Überschuss vorhanden ist, ob dieser entsprechend hoch ist und wie hoch dieser überhaupt ist, um damit die Wallbox anzusteuern. Fast alle Wallboxen können die Ladeleistung anpassen. Dabei wird dem Auto ständig mitgeteilt mit welcher Stromstärke es gerade laden darf. Eine Anpassung findet dabei teilweise im Sekundentakt statt. evcc hat alle Infos zu deiner Installation (PV Erzeugung, den SoC (State of Charge = Füllstand) eines eventuell vorhandenen Stromspeichers fürs Haus, den Verbrauch im Haus) und kann damit der Wallbox ständig mitteilen welche Leistung zum Laden zur Verfügung steht, ohne dass man dafür Strom aus dem Netz einkauft. Dabei wird nicht bei jeder kleinen Wolke direkt die Ladung unterbrochen, sondern evcc beobachtet z.B. für 2 Minuten wie sich der Überschuss verhält und schaltet lieber auf das Minimum von 6A. Wenn man dann einmal für 1 Minute Strom aus dem Netz bezieht, sind das wenige €cent. 
Diese €cent werden über die Zeit aber auch wieder wett gemacht, denn es wird Zeiten geben, wo man doch Strom ins Netz einspeist.

### wichtiges Wissen
Nun komme ich zu zwei weiteren Punkten, welche Vielen interessanterweise nicht bewusst, aber sehr wichtig sind.
 > 1 **Schieflast**<br>
 > 2 **Saldierende Stromzähler**<br>

In allen Ländern mit 3 Phasigen Stromsystem - wie wir in Deutschland - muss darauf geachtet werden, dass keine ***Schieflast*** im Stromsystem entsteht. Von einer Schieflast spricht man, wenn eine Phase stärker belastet wird als die anderen Phasen, also über z.B. über L1 mehr Strom fließt als über L2 und L3. In Deutschland ist dies auf 20A begrenzt. Man darf also auf einer Phase niemals 20A mehr Strom fließen haben, als auf den verbliebenen Phasen. Beim Auto laden wird dies nicht direkt als relevant ersichtlich, weil man meint mit 11KK Ladeleistung ja nur 16A pro Faser zu belasten, aber was ist mir den anderen Verbrauchern im Haus? Der Elektriker wird zwar versuchen die einzelnen Stromkreise auf die 3 Phasen symmetrisch aufzuteilen. Das Nutzungsverhalten kann aber dazu führen, das wenn mit 16A einphasig geladen wird, die Waschmaschine und das Kochfeld zum Mittagessen mehr als 4A, also mehr als 920W verbrauchen. Es muss also geschaut werden, dass man bei der einphasigen nicht an das Maximum geht. Dieses Problem löst sich aber gleich auf, denn kommen wir nun zu ***Saldierenden Stromzählern***:

Mir ist kein vom Netzbetreiber verbaute Smart Meter bekannt, welcher nicht nach dem Prinzip der ***Saldierung*** funktioniert. Im Grunde erschließt sich allen gleich was damit gemeint ist: Auf der Stromrechnung steht am Jahresende: Verbraucht wurden 6000kWh zu x,xx €Cent. Wie man sieht, ist das der Gesamtverbraucht. Hier steht nirgends auf L1 wurden 1000kWh, auf L2 wurden 3000kWh und auf L3 wurden 2000kWh verbraucht. Der Zähler zählt die Verbräuche der einzelnen Phasen zusammen. Was passiert aber mit der Einspeisung? Naja, auch diese wird zusammengerechnet und auch direkt abgezogen. Wie abgezogen? <br> Saldierende Stromzähler haben einen Zähler für den Netzbezug und einen für die Netzeinspeisung. Gehen wir z.B. davon aus, das aktuell auf jeder Phase 1000W verbraucht wird, dann zählt der Netzbezugszähler 3x1000W = 3000W Netzbezug. Zur gleichen Zeit erzeugt unsere kleine einphasige Solaranlage 4000W. Diese 4000W erfasst der Netzeinspeisungszähler. Nun stehen die 3000W Netzbezug, den 4000W Netzeinspeisung gegenüber und der Stromzähler saldiert dies direkt zu 1000w zugunsten unserer PV Anlage. Wäre die Netzeinspeisung in unserem Beispiel auf 3000W, würde sich der Zähler nicht drehen, um das mal Bildlich auf die alten schwarzen Zähler mit Drehscheibe zu projizieren. Es entstehen dadurch keine Kosten. 

### 1Phasig oder 3Phasig laden?
Gehen wir davon aus, dass wir nur 3Phasig laden, dann müsste unserer PV Anlage mehr als 3500W Überschuss zur Verfügung stellen, denn wir brauchen 6A pro Phase. Sinkt der Überschuss und bleiben dadurch weniger als 6A pro Phase über, kann es sein das das E-Auto aufhört zu laden. Gehen wir von 3500W Überschuss aus und schauen uns das als einphasige Ladung an, kommen wir mit dieser Leistung an die Belastungsgrenze der verwendeten Kabel und Sicherungen eine klassische Wallbox-Installation. Auch können die meisten 11kw Wallboxen max. 16A pro Phase =3650W einstellen. Da wir nun wissen das Saldierende Stromzähler sowieso alles direkt verrechnen, können wir die PV Anlage einfach einphasig ihre 3500W einspeisen lassen aber wir beziehen die eingespeiste Leistung 3Phasig zum Laden. Das schon die Technik und ist bei den meisten Autos der effizienteste Weg. Steigt nun aber unsere Überschussleistung auf z.B. 4000W, bekommen wir einphasig schnell das Problem, das wir dann Energie ins Netz einspeisen müssen, weil wir diese nicht mehr einphasig zur Wallbox bekommen.

### Phasenumschaltung
Wenn man dies etwas sacken lässt, wird schnell klar, warum das oben Beschriebene interessant und wichtig für den nächsten Abschnitt ist. <br>
Einige Wallboxen können eine sogenannte Phasenumschaltung durchführen. Dabei kann zwischen 1Phasigen und 3Phasigen Laden - mehr oder weniger - schnell umgeschaltet werden. Dabei ist einiges Seitens der Wallbox zu beachten - das das ist das Problem der Wallbox und nicht Unseres. Hat man so eine Wallbox, dann weiß evcc dies und kann das oben beschriebene Verfahrungen durchführen.

Hier sieht man, dass mehr Überschuss vorhanden ist, wie einphasig geladen werden kann:
![IMG_0671](https://user-images.githubusercontent.com/91694293/177996895-d7d576fd-9c1d-4e5c-8ec8-dc9c082ef846.jpg) 

Man sieht das nun 3Phasig mit 4,7kw geladen wird:
![IMG_0672](https://user-images.githubusercontent.com/91694293/177996923-1bb30b2b-bd25-4ee3-9150-09eb720f2b74.jpg)

Nach der Umschaltung auf den dreiphasigen Betreib, hat evcc kurze Zeit später erkannt, das nun nicht mehr genügend Überschuss vorhanden ist, um die mind. Stromstärke von 6A pro Phase zu halten. Es wird wieder auf 1Phasige Ladung umgestellt:
![IMG_0673](https://user-images.githubusercontent.com/91694293/177996933-726929f5-c3b9-44b8-a168-a80ddd816ec7.jpg) 
Sollte evcc während der "Wartezeit" wieder mehr Überschuss feststellen würde keine Umschaltung auf 1Phasig stattfinden.

### Nach der Theorie nun die Praxis
Für diese ganzen Infos braucht man bei evcc eine Komponente, die dem System sagt was denn aktuelle so los ist im eigenen Stromkasten. Diese Komponente nennt sich „grid“.<br>
Ein "grid" ist nichts anderes als ein Gerät was Informationen zum aktuellen Stromverbrauch und der aktuellen Einspeisung liefert. <br>
Bei vielen PV Anlagen ist ein solcher Stromzähler entweder schon enthalten oder wurde zusätzlich eingebaut. Entweder sitzt dieser direkte hinter dem Netzstromzähler und wurde zwischen Diesem und den Sicherungen „eingeschleift“. Oder er verfügt über Strommessklemmen. Dies sind kleine Spulen wo die Kabel vom Netzstromzähler pro Phase durchgeführt werden oder welche um die Kabel gelegt werden. So können diese den Strom messen, der durch die Adern fließt und zwar „Rein“ wie „Raus“.

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
Wichtig ist aber die Zeile `jq`. jq wird zum Filtern von Daten aus einem JSON Datagramm genutzt. Hier kommen nun wieder die oben beschrieben Variablen des Tasmota script ins Spiel. Das jq filtert auf `.StatusSNS.Strom.Power_cur` und dies findet sich im JSON Datagramm: {"`StatusSNS`":{"Time":"2022-07-06T21:00:25","`Strom`":{"Total_in":9982.810,"`Power_cur`":7,"Total_out":9055.830,"Meter_id":"090"}}} <br>

Ein JSON muss man sich wie ein Baum vorstellen. Oben steht `StatusSNS`. Darunter `Time` und `Strom`. <br>
```
|-StatusSNS
  |-Time
  |-Strom
    |-Power_cur
    |-Total_out
    |-Meter_id
```
Time hat hier einfach den Wert 2022-07-06T21:00:25. Strom hat aber wiederrum eine weitere Verzweigung `Power_cur`, `Total_out` und `Meter_id`. Um nun in diesem JSON Baum einen Punkt anzusteuern, geht man den Baum von oben nach unten ab und zweigt immer mit einem PUNKT ab bis man "angekommen" ist. 


Benennt man nun im Script die Variablen anders,

statt `+1,14,s,0,9600,Strom` z.B. `+1,14,s,0,9600,AUA` <br>
und statt `1,77070100010800ff@1000,Gesamt Verbrauch,KWh,Total_in,3` z.B. `1,77070100010800ff@1000,Gesamt Verbrauch,KWh,REIN,3` <br>
und statt `1,77070100100700ff@1,Aktueller Verbrauch,W,Power_cur,0` z.B. `1,77070100100700ff@1,Aktueller Verbrauch,W,REINuRAUS,0` <br>

müsste "jq" statt `.StatusSNS.Strom.Power_cur` dann `.StatusSNS.AUA.REINuRAUS` lauten.

Ich hoffe das war einigermaßen verständlich.
