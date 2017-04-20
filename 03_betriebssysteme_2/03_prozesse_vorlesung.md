# Prozesse und Scheduler
## Der Prozess
* Zentrales Konzept in jedem BS
* Abstraktion eines laufenden Programmes
* Warum ist das Konzept eines Prozesses wichtig?
## In einem Multitasking BS
* Jedes Programm läuft ca. 10 bis 100 Millisekunden
* D.h. pro Sekunde laufen mehrere Programme
* Quasiparallelität, da wir zu langsam sind, einen Unterschied zu erkennen

##Das Prozessmodell
###Zu einem Prozess gehören
* Das Programm
* Aktuelle Werte der Befehlszähler
* Registerinhalte
* Belegung aller Variablen
* Prozesskontrollblock (PCB)
* Geöffnete Dateien
* Benutze CPU Zeit
* Prozess ID
  _Man könnte noch für jeden Prozess eine virtuelle CPU dazuzählen_

##Prozesserzeugung
* Initialisierung des Systems
* Systemaufruf zum Erzeugen eines Prozesses durch einen anderen Prozess
* Benutzeranfrage, einen neuen Prozess zu erzeugen
* Initiierung einer Stapelverarbeitung (Batch-Job)

## Prozessunterscheidung
* Im Vordergrund – interaktiv
* Im Hintergrund – Service, Daemons, o.ä.
* Im Leerlauf

## Prozessbeendung
* Normales Beenden (freiwillig)
* Beenden aufgrund eines Fehlers (freiwillig)
* Beenden aufgrund eines schwerwiegenden Fehlers (unfreiwillig)
* Beenden durch einen anderen Prozess (unfreiwillig)

## Prozesszustände
* Rechnend
  Die Befehle werden in diesem Moment auf der CPU ausgeführt
* Rechenbereit
  Kurzzeitig gestoppt, um anderen Prozess rechnen zu lassen
* Blockiert
  Nicht lauffähig bis ein bestimmtes externes Ereignis eintritt

## Ablauf eines Prozesswechsels
* Hardware sichert Befehlszähler, etc.
* Hardware holt neuen Befehlszähler vom Interruptvektor
* Assemblerfunktion speichert Register
* Assemblerfunktion erzeugt neuen Kellerspeicher
* C-Unterbrechnungsroutine läuft (puffert Ein- und Ausgaben)
* Scheduler sucht nächsten Prozess
* C-Funktion kommt zur Assemblerfunktion zurück
* Assemblerfunktion startet neuen aktuellen Prozess

## Unterschied Prozess vs. Thread

* Threads sind leichtgewichtige Prozesse
* Entstehen innerhalb eines Prozesses
* Haben keinen eigenen Adressraum
* Einfachere Handhabung durch den Programmierer
* Gemeinsamer Speicherbereich
  _ABER VORSICHTIG! Gemeinsamer Zugriff auf Speicher bedeutet ..._ 
  _ Threadfähigkeit notwendig!_

## Hauptaufteilung Scheduler

### Non Preemptive / Kooperativ
_Nicht unterbrechende Scheduler lassen einen Prozess, nachdem ihm die CPU einmal zugeteilt wurde, solange laufen, bis dieser diese von sich aus wieder freigibt oder bis er blockiert._

### Präemptiv
_Unterbrechende Scheduler teilen die CPU von vornherein nur für eine bestimmte Zeitspanne zu und entziehen dem Prozess diese daraufhin wieder._

## Anforderungen an Prozessscheduler

* Stapelverarbeitungssysteme
  _Einfach: Ankommende Aufträge werden in eine Warteschlange eingereiht und jedes Mal, wenn ein Job abgearbeitet ist, kommt der nächste aus der Schlange dran (Queue-Manager)._ 

* Interaktive Systeme
  _Anforderungen Der Benutzer legt Wert auf kurze Antwortzeit. Wenn er beispielsweise in einem Texteditor eine Tastatureingabe tätigt, sollte der Text sofort erscheinen._

* Echtzeitsysteme
  _Gibt es für jeden Job ein Zeitlimit, bis zu dem die Aufgabe erledigt sein muss._



## Ziele von Schedulern

###Allgemein
* Fairness: Kein Prozess sollte unverhältnismäßig lange warten müssen, während ein anderer bevorzugt wird.
* Balance: Die Prozesse sollten der CPU auf eine Weise zugeteilt werden, dass auch andere Ressourcen wie Festplatte, Netzwerk Schnittstelle u.a. ausgelastet sind.
* Einhaltung der Systemregeln

### Ziele Stapelverarbeitungssysteme
* CPU-Auslastung: Die CPU sollte zu jeder Zeit ausgelastet sein. Es soll nicht vorkommen, dass die CPU sich im Leerlauf befindet, nur weil ein Prozess auf Daten von der Festplatte wartet.
* Durchsatz: Die Anzahl beendeter Aufgaben pro Zeiteinheit sollte maximiert werden. Dies ergibt eine ähnliche Strategie wie die Auslastung, betrachtet aber mehr das tatsächliche Ergebnis.
* kurze Turnaroundzeit (Durchlaufzeit): Die Zeit, die von der Ankunft eines Prozesses bis zu seiner vollständigen Beendigung vergeht, sollte minimiert werden.

### Ziele Interaktive Systeme (Dialogsysteme)
* Schnelle Antwortzeiten: Die Wartezeiten des Benutzers (oder anderer Systeme) sollten minimiert werden. Prozesse, die eine Interaktion mit dem Benutzer erfordern, sollten also bevorzugt werden vor solchen, die im Hintergrund stattfinden können.
* Proportionalität: Die Antwortzeiten verschiedener Prozesse sollten den Erwartungen des Benutzers entsprechen. Werden Prozesse (wie z. B. das Schließen einer Anwendung) vom Benutzer als simpel betrachtet, sollten diese auch schnell ausgeführt werden.

### Ziele Echtzeitsysteme

* Deadlines einhalten
* Vorhersagbarkeit: Wichtig für Multimedia-Anwendungen (da sonst z. B. Verschlechterung der Tonqualität droht) oder sicherheitskritische Anwendungen wie zum Beispiel Steuergeräte für Airbags, Tempomat bei Kraftfahrzeugen, oder Autopiloten bei Flugzeugen.

##Probleme von Scheduler

* die benötigten Betriebsmittel für die einzelnen Prozesse nicht im Vorfeld bekannt

* CPU Zeit - Scheduler muss dynamisch reagieren, Probleme sind:
  * Starvation
  * *Fairness


Schedulerstrategien: First In – First Out (FIFO), First-Come First-Served (FCFS)

alle Prozesse in der Reihenfolge ihres Eingangs bearbeitet

Eine Neuzuteilung der Prozesse findet erst statt, wenn der laufende Prozess zu warten beginnt oder seine Ausführung beendet ist

Erzielt eine gute Auslastung bezüglich der CPU

allerdings nicht bezüglich Ressourcen, die längere Zeit für eine Anforderung benötigen können z. B. Ein-/Ausgabe oder Massenspeicher

Für Mehrbenutzersysteme ist die Strategie darüber hinaus wenig geeignet, da einzelne Benutzer so ggf. für längere Zeit (nämlich bei aufwendigen Prozessen anderer Benutzer) ausgeschlossen werden.






Shortest-Job-Next (SJN), Shortest Job First (SJF), Shortest Processing Time (SPT)

Funktioniert nur, wenn die benötigte Rechenzeit für einzelne Aufgaben aus Erfahrungswerten gut vorhergesagt werden kann

Ein Nachteil ist, dass große Prozesse u. U. niemals die CPU zugeteilt bekommen, wenn sich immer kürzere Jobs vordrängeln

Können Prozesse unterbrochen werden, so dass ein Prozesswechsel durchgeführt wird, wenn ein neu ankommender Prozess eine kürzere Ausführungszeit aufweist als der aktuell laufende Prozess, so spricht man von Shortest-Remaining-Time (SRT) oder Shortest-Remaining-Processing-Time (SRPT).

nicht für Mehrbenutzersysteme geeignet






Earliest Due Date (EDD)

Die Prozesse zuerst ausgeführt, welche die geringste Deadline haben

Voraussetzung dafür sind statische Deadlines und gleichzeitiges Eintreffen voneinander unabhängiger Tasks

Dieses nichtunterbrechende Verfahren ist ideal, um die maximale Verspätung zu minimieren

Wenn Prozesse unterbrochen werden können spricht man von einer Terminabhängigen Ablaufplanung, Planen nach Fristen oder Earliest Deadline First (EDF)

Diese Strategie kommt hauptsächlich in Echtzeitsystemen vor, da es damit möglich ist, eine definierte Antwortzeit für bestimmte Prozesse zu garantieren.






Prioritätsscheduling

Bei dieser Strategie wird jedem Prozess eine Priorität zugeordnet. Die Abarbeitung erfolgt dann in der Reihenfolge der Prioritäten.

Rate Monotonic Scheduling (RMS): Die Priorität wird aus der Periodenlänge berechnet (Prozesse mit kürzeren Perioden haben höhere Priorität).

Deadline Monotonic Scheduling (DMS): Die Priorität wird aus der relativen Deadline berechnet (Prozesse mit kürzeren relativen Deadlines haben höhere Priorität).

Man kann auch mehreren Prozessen die gleiche Priorität geben, sie werden dann in Eingangsreihenfolge ausgeführt, oder mit einem untergeordneten Zeitscheibenverfahren innerhalb der gleichen Priorität abgewechselt (zum Beispiel Multilevel Feedback Queue Scheduling oder Shortest-Elapsed-Time (SET) )

Die Prioritäten können auch dynamisch sein, wobei sich die Priorität eines Prozesses mit der Zeit erhöht, damit auch niedrig priorisierte Prozesse irgendwann bearbeitet werden und nicht ständig von höher priorisierten Prozessen verdrängt werden.






Round Robin, Zeitscheibenverfahren

Einem Prozess wird die CPU für eine bestimmte (kurze) Zeitspanne zugeteilt.

Danach wird der Prozess wieder hinten in die Warteschlange eingereiht.

Sind die einzelnen Zeitspannen unterschiedlich groß, so spricht man von Weighted Round Robin (WRR)






Weitere Verfahren

Priority Scheduler

SRTF – Shortest Remaining Time First

Multilevel-Scheduler

Lotterie-Scheduler

Completly Fair Scheduler (CFS)

Multilevel feedback queue

…


