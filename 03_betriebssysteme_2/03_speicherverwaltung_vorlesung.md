# Speicherverwaltung

_Paging und Swapping_

## Wiederholung Speicherhierarchie

Cache-Speicher in der CPU

Level 2 und 3 Caches in oder außerhalb der CPU

Arbeitsspeicher (RAM)

SSDs

HDDs

Tapes

## Was hätten Programmierer gerne?

* Unendlich großer Speicher
* Unendlich schneller Speicher
* Nicht flüchtig (persistent)
* Billig
* Random Access

## Speicherverwaltung

__„Der Teil des Betriebssystems, der die Speicherhierarchie verwaltet, heißt Speicherverwaltung.“ – Tannenbaum (2003)__

### Aufgaben:

* Verfolgt welche Speicherbereiche genutzt werden (und welche nicht)
* Teilt Prozessen Speicher zu, wenn benötigt
* Nimmt freigewordenen Speicher von Prozessen zurück
* Verwaltet die Auslagerung von Speicher auf die Festplatte (Swapping)

## Entwicklung

* Früher sehr einfache Systeme
* Heutzutage wichtiges, kompliziertes Gebilde
* Warum dann noch die alten Systeme anschauen?
* Geschichte wiederholt sich!
* iOS / Android Speicherverwaltung am Anfang sehr einfach, wird jetzt wieder komplexer!

## Klassen von Speicherverwaltung

* Verschiebt Speicher zwischen den Speicherhierarchien
* Verschiebt keinen Speicher

_Das war ja einfach ;-)_

_Warum ist es überhaupt notwendig?_

* Arbeitsspeicher &lt; als alle laufenden Prozesse!

* Wenn wir mehr Arbeitsspeicher haben als wir Programme laufen lassen wollen, brauchen wir keine Speicherverwaltung

* Alle Programme würden im Speicher liegen

  Zusätzlich:

  ​	Relokationsproblem

  ​	Speicherschutz

## Speicherverwaltung I: Monoprogrammierung ohne Auslagerung oder Seitenverwaltung

Es wir nur ein Programm ausgeführt

...

das ist (im Prinzip) alles

Drei Varianten:

* BS liegt im RAM =&gt; erste Mainframes und Mini Computer


* BS liegt im ROM =&gt; Palmtops und embedded Systems
* BS liegt im RAM, Gerätetreiber im ROM =&gt; frühe PCs mit DOS und BIOS

## Speicherverwaltung II: Multiprogrammierung mit festen Partitionen

Aufteilung des gesamten Speichers in festen Partitionen variabler Größen

Z.B. Festlegung beim Systemstart (IBM OS/360)

Für jede Partition wird eine Warteschlange eingerichtet

Alternativ eine Warteschlange für alle Partitionen, wenn eine Partition frei wird, wird der nächste „passende“ Prozess ausgeführt

Nur Prozesse einer bestimmten Größe dürfen in die Partitionen (kleine nicht in die großen Partitionen, etc.)

## Speicherverwaltung III: Swapping

Ablauf:

Jeder Prozess wird nach Aufkommenszeit komplett in den Speicher geladen

Dann läuft dieser Prozess eine gewisse Zeit

Wenn ein neuer Prozess Speicher benötigt, wird der „älteste Prozess“ gewählt

Anschließend wird er auf die Festplatte ausgelagert

Besser als feste Partitionen, da nur tatsächlich benötigter Speicher genutzt wird

Problem: Speicherlöcher

## Speicherverwaltung IV: Virtueller Speicher mit Paging

Idee des virtuelle Speichers erlaubt Programme die größer sind als der vorhandene Arbeitsspeicher

BS hält Teile eines Programms im Arbeitsspeicher

Der Rest bleibt auf der Festplatte

Beispiel: 4MB Arbeitsspeicher, 16MB Programm, es werden nur 4MB in den Arbeitsspeicher geladen

Wenn ein anderer Teil des Programms benötigt wird, wird dieser in den Speicher geladen und ein anderer Teil wieder ausgelagert

## Paging mit MMU

Bisher: direkter Zugriff auf echte Speicherbereiche des Systems

Jetzt: Zugriff auf Adresse wird abgefangen und an die MMU weitergeleitet

_MMU: Memory Management Unit_

Dieser bildet einen eigenen virtuelle Speicherraum für jedes Programm

Setzt dann diese virtuelle Speicher optimal auf den echten Speicher um



### Paging Begrifflichkeiten

* Der virtuelle Adressraum wird in Einheiten aufgeteilt: Seiten (Pages)

* Die Einheit im physikalischen Speicher heißt Seitenrahmen (page frames)

  _Beide sind natürlich gleich groß_



## Beispiel eines Paging-Zugriffs

Ein Programm will auf Adresse 0 zugreifen

Wird an die MMU geschickt

Adresse null gehört zur Page 0 (Adressen 0 – 4095)

Entspricht bspw. des Pageframe 2 (8192 – 12287)

Gibt die Adresse 8192 an den Speicherbus weiter

## Was passiert wenn auf Speicherbereiche zugegriffen werden, die nicht im Speicher liegen?

MMU stellt fest, die Seite liegt nicht im Speicher

Löst einen Systemcall auf, der Seitenfehler genannt wird

Das BS sucht sich einen wenig benutzten Seitenrahmen im Speicher aus und schreibt ihn zurück auf die Platte

Dann lädt es die Seite, die den Seitenfehler verursacht hat in den frei gewordenen Seitenrahmen

(Muss noch die Seitentabelle anpassen ... Details ... )

Führt den Befehl noch einmal aus

## Seitenersetzungsalgorithmen

Im BS gleiches Problem wie im CPU Cache oder bei einem Webserver:

* welche Seiten sollte ich im Speicher lassen
* welche sollte ich aus dem Speicher nehmen
* welche werden voraussichtlich als nächste aufgerufen

### Not-Recently-Used

„Seiten die in letzter Zeit nicht genutzt wurden“

Einfach zu implementieren, wertet R/M-Bits (Referenced-/Modified) aus

M-Bit wenn der Seitenrahmen sich verändert hat R-Bit wenn lesend oder schreiben zugegriffen wurde

Setzen i.d.R. durch HW

Zurücksetzen durch Software (Kernel) in bestimmten Zeitabständen

### First in First out (FIFO)

Der älteste Seitenrahmen wird als erstes ersetzt Statt M/R-Bit einen Zeitstempel

Einfach zu implementieren

Nachteil: keine Rücksicht auf die Nutzung

In der Praxis selten eingesetzt

### Least Recently Used

Es wird die am wenigsten genutzte Seite ausgelagert (R-Bit)

Recht nahe am optimalen Algorithmus und aufwändig zu realisieren, da bei jedem Zugriff die komplette Liste der neu bewertet werden muss.

Am einfachsten ist eine nach der Nutzung sortierte Liste.

Implementiert werden heute meistens

Pseudo-LRU-Algorithmen

(mit M/R-Bit), s.o.

Clock- Page

und

Second Chance

### NFU (Not Frequently Used)

Die seltensten benutzten Seiten werden ausgelagert

Alternative zu LRU (mit passenden Aging-Algorithmus)

Seitentabelleneintrag mit Zähler für Zugriffe

Positiv: weniger Umhängeoperationen als bei LRU

Negativ: älterer vorher frequentierte Einträge werden nicht ausgelagert (Aging)

=> Übungsaufgaben

## Shared Memory

Durch ein virtuelles Speichermodell wird auch die gemeinsame Nutzung eines Speicherbereichs durch mehrere Prozesse ermöglicht.

Dieser Speicher kann Read-Only (Shared-Library/DLL) oder auch zur Interprocesscommunication (IPC) genutzt werden.

Gemeinsame Codeteile müssen wiedereintrittfähig (reentrantfähig/ threadsafe) sein!

Gemeinsamer Speicher muss per Semaphoren, Monitore geschützt werden.



