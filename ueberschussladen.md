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

In allen Ländern mit 3 Phasigen Stromsystem - wie wir in Deutschland - muss darauf geachtet werden, dass keine ***Schieflast*** im Stromsystem entsteht. Von einer Schieflast spricht man, wenn eine Phase stärker belastet, wird als die anderen Phasen, also über z.B. über L1 mehr Strom fließt als über L2 und L3. In Deutschland ist dies auf 20A begrenzt. Man darf also auf einer Phase niemals 20A mehr Strom fließen haben als auf den verbliebenen Phasen. Beim Auto laden wird dies nicht direkt als relevant ersichtlich, weil man meint mit 11KW Ladeleistung ja nur 16A pro Phase zu belasten, aber was ist mit den anderen Verbrauchern im Haus? Der Elektriker wird zwar versuchen die einzelnen Stromkreise auf die 3 Phasen symmetrisch aufzuteilen. Das Nutzungsverhalten kann aber dazu führen, dass wenn mit 16A einphasig geladen wird, die Waschmaschine und das Kochfeld dummerweise ebenfalls auf dieser Phase liegen und zum Mittagessen mehr als 4A, also mehr als 920W verbrauchen. Es muss also geschaut werden, dass man bei der einphasigen nicht an das Maximum geht. Dieses Problem löst sich aber gleich auf, denn kommen wir nun zu ***Saldierenden Stromzählern***:

Mir ist kein vom Netzbetreiber verbaute Smart Meter bekannt, welcher nicht nach dem Prinzip der ***Saldierung*** funktioniert. Im Grunde erschließt sich allen gleich was damit gemeint ist: Auf der Stromrechnung steht am Jahresende: Verbraucht wurden 6000kWh zu x,xx €Cent. Wie man sieht, ist das der Gesamtverbrauch. Hier steht nirgends auf L1 wurden 1000kWh, auf L2 wurden 3000kWh und auf L3 wurden 2000kWh verbraucht. Der Zähler zählt die Verbräuche der einzelnen Phasen zusammen. Was passiert aber mit der Einspeisung? Naja, auch diese wird zusammengerechnet und auch direkt abgezogen. Wie abgezogen? <br> Saldierende Stromzähler haben einen Zähler für den Netzbezug und einen für die Netzeinspeisung. Gehen wir z.B. davon aus, das aktuell auf jeder Phase 1000W verbraucht wird, dann zählt der Netzbezugszähler 3x1000W = 3000W Netzbezug. Zur gleichen Zeit erzeugt unsere kleine einphasige Solaranlage 3500W. Diese 3500W erfasst der Netzeinspeisungszähler. Nun stehen die 3000W Netzbezug, den 3500W Netzeinspeisung gegenüber und der Stromzähler saldiert dies direkt zu 500w zugunsten unserer PV Anlage. Wäre die Netzeinspeisung in unserem Beispiel auch 3000W, würde sich der Zähler nicht drehen, um das mal Bildlich auf die alten schwarzen Zähler mit Drehscheibe zu projizieren. Wie beim Bankkonto: Einnahmen und Ausgaben ergeben den Saldo - dabei ist es egal ob ich große Einnahmen und viele kleine Ausgaben oder umgekehrt habe, alles wird zusammengerechnet und ergibt dann den Saldo.

### 1Phasig oder 3Phasig laden?
Gehen wir davon aus, dass wir nur 3Phasig laden, dann müsste unserer PV Anlage mehr als 3500W Überschuss zur Verfügung stellen, denn wir brauchen ja immer die "6A" pro Phase. Sinkt der Überschuss und bleiben dadurch weniger als 6A pro Phase über, kann es sein das das E-Auto aufhört zu laden. <br>
Gehen wir einmal von den besagten 3500W Überschuss aus und schauen uns das als **einphasige** Ladung an, kommen wir mit dieser Leistung an die Belastungsgrenze der verwendeten Kabel und Sicherungen eine klassische 11kw Wallbox-Installation. Auch können die meisten 11kw Wallboxen max. 16A pro Phase = 3650W einstellen. Da wir nun wissen das Saldierende Stromzähler sowieso alles direkt verrechnen, können wir die PV Anlage einfach ihre 3500W **einphasig** einspeisen lassen, wir beziehen die eingespeisten 3500W Leistung aber **3Phasig** über die Wallbox zum Laden. Das schont die Technik (weniger starke Belastung der Leitungen und Sicherungen) und ist nebenbei für den meisten Autos der effizienteste Weg.<br>
Steigt nun aber in unserem Beispiel die Überschussleistung der PV Anlage auf z.B. 4000W, bekommen wir einphasig schnell das Problem, das wir dann Energie ins Netz einspeisen müssen, weil wir diese nicht mehr einphasig zur Wallbox bekommen. Hier kommt die Phasenumschaltung ins Spiel:

### Phasenumschaltung
Wenn man dies etwas sacken lässt, wird schnell klar, warum das oben Beschriebene interessant und wichtig für den nächsten Abschnitt ist. <br>
Einige Wallboxen können eine sogenannte ***Phasenumschaltung***durchführen. Dabei kann zwischen **einphasigen** und **3Phasigen** Laden - mehr oder weniger - schnell umgeschaltet werden. Dabei ist einiges Seitens der Wallbox zu beachten - aber dies ist das Problem der Wallbox und nicht Unseres. Hat man so eine Wallbox, dann weiß evcc dies und kann das oben beschriebene Verfahrungen durchführen: Die Ladung wurde durch die anfänglichen 3500W Überschuss **einphasig** begonnen und wird bei 4000W Überschuss auf eine **3Phasige** Ladung umgestellt.

Um das zu verdeutlichen, hier ein paar Bilder aus der Praxis.

Anfänglich wurde mit ca. 3kw Überschuss geladen. kurze Zeit später stieg der Überschuss auf 3,8KW. Nun kann nicht mehr **einphasig** geladen werden und evcc bereitet die Umschaltung auf **3Phasige** Ladung vor. Auch zu sehen ist, dass zum Zeitpunkt des Screenshots kurz die PV Leistung abgesunken ist und etwas Netzbezug stattgefunden hat:
![IMG_0671](https://user-images.githubusercontent.com/91694293/177996895-d7d576fd-9c1d-4e5c-8ec8-dc9c082ef846.jpg) 

Man sieht das nun **3Phasig** mit 4,6kw geladen wird, der Überschuss aber zum Zeitpunkt des Screenshots nicht mehr ausreicht, und auch schon aus dem Netz und aus der Batterie nachgesteuert werden muss. Also bereitet evcc wieder die Umschaltung auf die **einphasige** Ladung vor, denn damit ist der Überschuss effektiver zum Auto zu bringen:
![IMG_0672](https://user-images.githubusercontent.com/91694293/177996923-1bb30b2b-bd25-4ee3-9150-09eb720f2b74.jpg)

Nun ist der PV Ertrag zwar wieder auf 4,4kw angestiegen, aber in der Zwischenzeit musste die Hausbatterie Zuviel Leistung abgeben und hat einen SoC erreicht wo evcc gegensteuert. Es bereitet also wieder die Umschaltung auf die **einphasige** Ladung vor und hat auch schon die Stromstärke in der Wallbox verringert. Damit steht schon wieder Strom zur Verfügung welcher genutzt wird, um den Haus Akku zu laden. Im evcc Lademodus *PV* hat der Haus Akku immer Vorrang vor dem Auto Akku: 
![IMG_0673](https://user-images.githubusercontent.com/91694293/177996933-726929f5-c3b9-44b8-a168-a80ddd816ec7.jpg) 
Sollte evcc während der "Wartezeit" wieder mehr Überschuss feststellen würde keine Umschaltung auf 1Phasig stattfinden.

### Nach der Theorie nun die Praxis
Für diese ganzen Steuerung braucht es Informationen aus allen Richtungen. Insbesondere braucht es bei evcc eine Komponente, die dem System sagt, was denn aktuelle so los ist im eigenen Stromkasten in Bezug auf Netzbezug und Netzeinspeisung. Diese Komponente nennt sich „grid“.<br>
Ein "grid" ist also nichts anderes als ein Gerät was Informationen zum aktuellen Stromverbrauch und der aktuellen Einspeisung liefert. <br>
Bei vielen PV Anlagen ist ein solcher Stromzähler entweder schon enthalten oder wurde zusätzlich eingebaut. Entweder sitzt dieser direkte hinter dem Netzstromzähler und wurde zwischen Diesem und den Sicherungen „eingeschleift“. Oder er verfügt über Strommessklemmen. Dies sind kleine Spulen wo die Kabel vom Netzstromzähler pro Phase durchgeführt werden oder welche um die Kabel gelegt werden. So können diese den Strom messen, der durch die Adern fließt, und zwar „Rein“ wie „Raus“.

Hat man so etwas nicht, oder eine Balkonanlage oder eine Selbst gebaute PV Anlage oder einfach so einen ESPLesekopf, kann man auch am Netzstromzähler diese Daten abgreifen. Diese Smart Meter wurden oben beschrieben. Sie erfassen nicht nur das „Rein“ wie „Raus“, sondern ganz wichtig: An diesen Netzstromzähler wird schließlich gezählt was man bezahlt.

Als "grid" kann also auch der Lesekopf dienen, wenn er z.B. mit Tasmota läuft. 
Tasmota kann über eine http api die Werte des Stromzählers als json ausgeben. Der Aufruf erfolgt mit `http://ipadressedeslesekopf/cm?cmnd=status%208`. <br>
Abhängig vom verwendeten Script, erhält Jeder zwar die Daten aber mit den Bezeichnungen, welche im Script verwendet wurden.

Ein Beispiel:
Ein typisches Script für den ESPLesekopf sieht so aus:
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
Zu erkenne ist, das in den Zeilen mit 1,.... an vorletzter Stelle die Bezeichnung steht. Einmal `Total_in`, einmal `Power_cur`, `Total_out` und `Meter_id`

Die Ausgabe von `.../cm?cmnd=status%208` sieht so aus:<br>
`{"StatusSNS":{"Time":"2022-07-06T21:00:25","Strom":{"Total_in":9982.810,"Power_cur":7,"Total_out":9055.830,"Meter_id":"090"}}}`

Zu erkennen ist der Aufbau des JSON Datagram. `1,77070100010800ff@1000,Gesamt Verbrauch,KWh,Total_in,3` im Script erzeugt eine Variabel "Total_in". Diese findet sich auch im JSON wieder. Auch "Power_cur" aus der Script Zeile `1,77070100100700ff@1,Aktueller Verbrauch,W,Power_cur,0` findet sich wieder. Da jeder sein Script vielleicht anders schreibt und auch andere Variablen nutzt ist das Ganze für evcc nicht nach Schema F zu bewältigen. Es gibt zwei Vorgaben von evcc wie die einzelnen Werte zu heißen haben, aber wer will sich schon etwas vorschreiben lassen und manchmal braucht man einfach seine eigenen Bezeichnungen. Also müssen wir evcc nachhelfen.

Schauen wir uns nun die evcc.yaml Konfigurationsdatei an, zumindest die hier relevanten Abschnitte:
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

Es werden mit dem Abschnitt **"meters:"** die ganzen Stromzähler definiert. Hier kommt nun das http PlugIn von evcc und die damit mögliche Abfrage von Tastmota zum Tragen. (PlugIn bedeutet hier nicht, das irgendetwas nachinstalliert werden muss, es ist einfach dabei. Warum es dann PlugIn heißt...) In diesem Beispiel hat der Lesekopf die IP 192.168.178.77.<br>
Wichtig ist aber die Zeile `jq`. **jq** wird zum Filtern von Daten aus einem JSON Datagramm genutzt. Hier kommen nun wieder die oben beschrieben Variablen des Tasmota script ins Spiel. Das **jq** filtert in meiner Konfiguration auf `.StatusSNS.Strom.Power_cur` und dies findet sich im JSON Datagramm: {"`StatusSNS`":{"Time":"2022-07-06T21:00:25","`Strom`":{"Total_in":9982.810,"`Power_cur`":7,"Total_out":9055.830,"Meter_id":"090"}}} <br>

Ein JSON Datagramm muss man sich wie ein Baum vorstellen. Oben steht in unserem Fall `StatusSNS`. Darunter `Time` und `Strom`. <br>
```
|-StatusSNS
  |-Time
  |-Strom
    |-Power_cur
    |-Total_out
    |-Meter_id
```
Time hat hier einfach den Wert 2022-07-06T21:00:25. Strom hat aber wiederrum eine weitere Verzweigung `Power_cur`, `Total_out` und `Meter_id`. Da diese keine weiteren Abzweigungen haben, enthalten `Power_cur`, `Total_out` und `Meter_id` Daten in welcher Form auch immer. Um nun in diesem JSON Baum einen Punkt anzusteuern, geht man den Baum von oben nach unten ab und zweigt immer mit einem PUNKT ab bis man "angekommen" ist: `.`***StatusSNS`.`Strom`.`Power_cur***


Benennt man nun im Script die Variablen anders,

statt `+1,14,s,0,9600,Strom` z.B. `+1,14,s,0,9600,AUA` <br>
und statt `1,77070100010800ff@1000,Gesamt Verbrauch,KWh,Total_in,3` z.B. `1,77070100010800ff@1000,Gesamt Verbrauch,KWh,REIN,3` <br>
und statt `1,77070100100700ff@1,Aktueller Verbrauch,W,Power_cur,0` z.B. `1,77070100100700ff@1,Aktueller Verbrauch,W,REINuRAUS,0` <br>

müsste "jq" statt `.StatusSNS.Strom.Power_cur` dann `.StatusSNS.AUA.REINuRAUS` lauten.

`.StatusSNS` kann man nicht selbst beeinflussen. Dies gibt Tasmota vor. Selbst wenn Tasmota hier Änderungen vornimmt, ein Aufruf von `http://ipadressedeslesekopf/cm?cmnd=status%208` zeigt das JSON Datagramm. Einfach die evcc.yaml anpassen und nach Neustart funktioniert wieder alles.

Im Abschnitt Site möchte evcc nun alle Komponenten mitgeteilt bekommen, die es verwenden soll.
```
site:
  title: Home
  meters:
    grid: lesekopf
```
Dies ist nur ein Auszug des `site:` Abschnittes. Es soll verdeutlichen, das evcc als grid Meter den oben definierten lesekopf nutzt und damit den Daten vom Smart Meter.

Ich hoffe das war einigermaßen verständlich.
