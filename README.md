Bei Fragen, Problemen oder Kommentare bitte unter Issues oder bei Kommentare. Danke.

🔷 Zpro1 v2.7.8– Moderne Mobile CPU-Architektur (2025)

Zpro1 v2 ist eine hochmoderne, realistisch umsetzbare CPU-Architektur für Smartphones und mobile Geräte, die Leistung, Effizienz und Systemintelligenz optimal verbindet. Sie basiert auf 12 physischen Kernen, innovativen Software-Fusionstechniken und einer tief integrierten Decoder-Steuerung.


---

1. Kernstruktur

12 reale Kerne, in 4 Cluster aufgeteilt:

3x Performance (4,32 GHz)

3x Balanced (3,5 GHz)

3x Effizienz (2,5 GHz)

3x Energiespar (1,9 GHz)



---

2. Virtuall Thread Duplicating and Fusion System (VTDFS)
!!!Funktioniert vollständig im Kernel!!!

Jeder Kern kann sich dynamisch in 2 virtuelle Threads aufteilen.

Threads bearbeiten unterschiedliche Aufgaben. TDFS aktiviert sich nur in fällen, wo viel paralelisierung gebraucht wird, oder viel Singelcore leistung gebraucht wird. dafür wird MCF verwendet.

Gesteuert von Gruppierung der leistung: bei niedrige paralelisierungs/leistungsintensive zb apps wird es minimal bis garnicht aktiviert. bei mittleren wird es im mittleren modus aktiv, und bei hohen im hohen modus.

TDFS darf nur Kerne von grupierten Cluster nehmen, um instabilität zu vermeiden.

ALUs Problem behoben: jeder kern hat 4 ALUs. sobald VTDFS anfängt, ist es ein ALU pool, aus dem jeder Thread dynamisch nehmen kann. wenn jedoch ein thread zu wenig ALUs kriegt, wird der sofort deaktiviert, bis er wieder genug hat, um keine FPS drops zu sehen. der noch aktive thread arbeitet dann mit allen ALUs, die er bereitgestellt hat, mitbezogen die von dem deaktivierten Thread, um fast die gleiche leistung zu erziehlen.
Wie Virtuelle Threads die stärke von z.b. ALU bekommt, geht das so: der ALU ist mit der CPU physisch verbunden, und einer von 2 TDFS Threads nutzt zb diesen ALU. Die 2 threads haben wie gesagt 4 ALUs zur Verfügung.

Ermöglicht fein abgestimmtes Multitasking mit minimalem Overhead.

Wie funktioniert die duplizierung der Threads?
Der Kernel wird so angepasst, das sich ein Thread verdoppeln kann, indem es virtuell einen zweiten erstellt, der aber dann die hälfte leistung des eigentlichen Threads nimmt, das sie 50/50 leistung vom eigentlichen haben. daas geschieht, wenn viel paralelisierung gebraucht wird. siehe MCF für TDFS leistungssteigerung.
Aber warum 50/50 leistung des eigentlichen Kerns?
wenn ein weiterer VT (Virtuall Thread) für einen Kern erstellt wird, ist es nicht wie Hyperthreading, indem ein kern für 2 arbeitet, sondern ein bischen anders. Der zweite VT nutzt die hälfte leistung des anderen VTs, indem die GHz leistung von dem ersten Thread virtuell zum anderen halbiert wird, was es ermöglicht, das z.b. ein kern mit 3.5 Ghz gleich 2 Threads mit jeweils 1,75 Virtuell zugeteilte GHz.

3. CPALU – Connected Parallel ALU

Die CPALU ist eine moderne ALU-Architektur, die aus vier Hauptblöcken besteht:

Logic (logische Operationen)

Adder (Addition/Subtraktion)

Shifter (Bitverschiebung/Rotation)

Multiplexer (wählt das Endergebnis)


Jeder Block ist physisch einzeln aufgebaut und über Transistorleitungen direkt miteinander verbunden.

Zusätzlich ist jeder Block in 4 kleinere Einheiten (Sub-Blöcke) unterteilt. Diese übernehmen Teilaufgaben, z. B.:

Addierer rechnet Bits 0–7, 8–15 usw.

Logik-Block hat eigene Einheiten für AND, OR, XOR,

So können Teile der ALU unabhängig oder parallel arbeiten.
Nicht genutzte Teile gehen in den Leerlauf, um Strom zu sparen.


4. VTALU (virtuall ALU) Technologie

Jeder CPU-Kern kann sich mit VTDFS wenn gebraucht(!!!) bis zu 4 virtuelle Threads (VTs) erzeugen, die an die MCF-Virtuell-ALU Einheit (virtuell) geschickt werden. Dort werden diese VTs zu virtuellen ALUs (VTALUs) gruppiert, die parallel an unterschiedlichen Teilen einer Aufgabe arbeiten. Die Arbeit wird in 2 bis 4 Threads aufgeteilt, die synchronisiert laufen: Jeder Thread bearbeitet zb ein Viertel Teil der Aufgabe und wartet, bis alle fertig sind, bevor das Ergebnis zusammengeführt wird. Die Anzahl der aktiven VTs passt sich dynamisch an die aktuelle Leistungslast an, sodass Ressourcen effizient genutzt und Energie gespart werden. Das bedeutet, wenn nur zb 2 VTALU Threads für das System benötigt wird, werden dann nur 2 benutzt.

MERKEN:
VTALU wird aber nur in fällen aktiviert, wo zu viele ALUs für Physisch benutzt werden müssen, und deswegen wird dann die CPU leistung dann etwas schwächer, aber die ALU leistung stärker.
Außerdem hat ein VTALU nicht die ganz volle Leistung eines Physischen ALUs, aber ist auch nicht viel schwächer. vielleicht hat ein VTALU 80-90% der leistung von einer Physischen ALU. VTDFS ist auch für die paralelisierung der CPU eigentlich da, also ist VTALU nur manchmal in fällen wo gebraucht aktiv. VTALUs nutzen auch die leistung eines VThreads von VTDFS.



5. Multi-Core Fusion (MCF) – Softwarebasierte Thread-Bündelung

MCF Nimmt 2 bis 4 virtuelle Threads aus TDFS und gruppiert sie als einen logischen „Superkern“. Die threads Arbeiten dann zusammen, und werden eine anforderung gegeben, hoch in taktraten zu arbeiten, weil sich MCF nur in fällen aktiviert, wo viel leistung gebraucht wird. dies kann passieren, indem die MCF kerne/threads eine höhere grenze der niedrigsten taktrate gestellt wird. Zb normale grenze 300mhz, mit mcf Kernen aber dann zb 1000. Das war ein Beispiel.

Das Betriebssystem sieht diese Gruppe als einen einzelnen Kern. Es täuscht das System nicht, sondern das System packt sie in einer einkern gruppe rein, wo sie als einen benutzt werden.

Info: ES kann nur einen Supercore geben.

Erhöht die Single-Core-Leistung.

Realistisch und energieeffizient, ohne thermische Probleme.

Cache Kohärenz Problem gelöst (neues update!):
Sobald VTDFS oder MCF, algemein virtuelle Threads benutzt werden, wird der sogenannte Actuell Information fetcher (AIF) aktiv. Der fetcht immer aktuelle informationen raus, und stellt sie für alle VTs aus. Jeder VT muss dann nach jeder berechnung immer in diesem Virtuellen AIF schauen, um aktuelle infos zu sammeln. Er ist spezialisiert, Daten zu fetchen und preiszugeben, mehr macht er nicht.

Aber was ist, wenn 2 apps MCF brauchen?
Das wird gelöst, indem man den einen Supercore gibt, der mehr leistung braucht. Der andere kriegt VTDFS.
Und wenn beide gleiche leistung brauchen, wird random entschieden, wer MCF kriegt, der andere kriegt dann einfach VTDFS.

6. Core Spannungs Anpassung (CSA)
CSA ist ein intelligentes System zur dynamischen Anpassung der Kernspannung und -taktung in der Zpro1-CPU. Es sorgt für eine optimale Balance zwischen Leistung, Energieverbrauch und thermischem Management.

Funktionsweise:
Im ruhigen Modus (Idle oder leichte Apps/Spiele):
CSA passt die Spannung und den Takt auf Cluster-Ebene an — einzelne Cluster können hoch- oder runtergespannt werden, je nachdem wie viel Leistung gerade benötigt wird.

Im mittleren und hohen Modus:
CSA passt Spannung und Takt auf einzelner Kern-Ebene an, um fein abgestimmte Leistungskontrolle zu ermöglichen.

Wichtige CSA-Regeln:
Keine Isolation:
In der Spannungsanpassung dürfen Kerne nicht „alleine“ hochgetaktet sein, während andere Kerne im selben Cluster im Idle-Modus sind.

Lastverteilung im mittleren Modus:
Mindestens mehrere Kerne in einem Cluster haben eine Last von mindestens 20 %. Einige Kerne dürfen bis zu 80 % hochgetaktet werden, aber nicht mehr.

Überschreitung der 80 %-Grenze:
Sobald einzelne Kerne über 80 % Last laufen, wird das Multi-Core Fusion (MCF) aktiviert, um die Kerne zu bündeln und die Leistung als „Superkern“ effizient zu steigern.

Vorteile von CSA:
Verhindert thermische Hotspots durch gleichmäßige Last- und Spannungsverteilung.

Spart Energie durch bedarfsgerechte Spannung und Takt.

Erhöht die Stabilität und Lebensdauer der CPU.

Unterstützt optimal die Leistungsschübe durch MCF, ohne dass einzelne Kerne unnötig überhitzen.



---

7. Cache-System mit virtuell integriertem AIF (Actuell Information Fetcher) -Cache (neu!)

siehe beschreibung bei MCF*.


9. Background App Thread (BAT)

TDFS BAT ermöglicht parallele Verarbeitung von Vordergrund- und Hintergrundaufgaben auf demselben/mehreren Kern/en. Beispielsweise wenn man eine app nutzt, und im hintergrund eine läuft.
---

8. Fast Efficient Architecture (FEA)

Dynamische Abschaltung oder Drosselung von Kernen bei hoher Last (>8W).

Sanfte Lastanpassung ohne FPS-Drops.

Die CPU kann Temperaturen von einzelnen Kernen messen, und die leistungsverteilung anpassen. Wenn zum Beispiel ein primekern voll ausgelastet ist, kommt TDFS MCF ins spiel, um threads von zum Beispiel medium kernen zu fusionieren, um die ähnliche leistung zu gefährleisten, während der voll ausgelastete prime kern ausgeschaltet wird, bis er abgekühlt wurde.




---

9. Kühlsystem (Update)
tief im system, wird ein Hitzewächter hinzugefügt, und zwar im Kernel.
Dieser virtuelle Hitzewächter heißt DOCU (Dynamic Overall Cooling Unit).
Der hat die Informationen von Temperaturen des Akkus, CPU und anderen Hitzentwickelnden Komponennten.
Er sammelt dann diese Ergebnisse, analysiert sie, und passt die leistungsverteilung an. Wenn beispielsweise der Akku c.a. 35-37°C ist, während die CPU Kühler ist, wird CSA (Core Spannungs Anpassung) die Information geschickt, hochbenutzte Kerne ehr abzutragen und dafür die effizienteren mittel-effizients Kerne zu nutzen.
Dazu gibt es noch ein Vapor chamber über der CPU, die es wie andere Handys Kühlen.



---

10. Zren X GPU (groß-update):
Die GPUs von mobilen geräten verfügen über hunderte von Shadereinheiten und "ALUs".
Darf ich vorstellen: Zren X
Zren X hat auch hunderte von Shadereinheiten, genauer gesagt 370, und hat 5 shader gruppen mit jeweils 74, hat jedoch die VSDS (Virtuall Shader Duplicating System) funktion.
Diese funktion kann jeweilige gruppen, also dann alle 74 shader einheiten wie VTDFS teilen, funktioniert ähnlich wie VTDFS, nur auf schader, und nicht einzelne Kerne, sondern von gruppen.
es ist ähnlich wie SMT oder Hyperthreading, nur arbeitet nicht ein shader kern für zwei VSs (Virtuall Shaders), sondern der zweite für sich selbst, nur halt von leistung her angetrieben von einem shader Kern.
Da jede shader gruppe sich "teilen" kann, hätte man theoretisch 740 shader einheiten.
Damit aber möglichst kein Overhead wegen der teilung geschieht, ist es so, das bei der Branch prediction geschaut wird, ob wahrscheinlich was leistungsaufwendiges passiert. Dann werden alle shader kerne, die gerade nicht genutzt werden, aufgefordert, bevor das grafikaufwendige geschieht, sich zu teilen. wenn es dann gerade dabei ist, grafikintensiv zu sein, werden auch die benutzten geteilt. dies ermöglicht ungefähr die hälfte Overhead, da die ungenutzten es nicht wirklich für die anwendung erzeugen können.



10. Wie kann man es ins Kernel einfügen? 
Spezieller code wird zuerst für TDFS geschrieben (bei den steuerungs Dateien geschrieben bei Github), und ist dann eine erweiterung der thread steuerung. Die thread steuerung davor wird dann auch optimiert für TDFS, damit sie optimal zusammen arbeiten können.
---

11. Zusammenfassung und Vorteile

Innovativ: Softwarebasierte Fusion (MCF) und dynamisches Thread-Management (TDFS).

Effizient: Intelligente Steuerung durch NPU und FEA.

Leistungsstark: Hohe Single- und Multi-Core-Leistung.

Flexibel: Paralleles Multitasking mit BAT.

Realisierbar: Vollständig umsetzbar mit aktueller 2025-Technologie.
Entwickelt von mir mit 11 jahren.
Diese Entwicklungsnotiz ist auch ein beweis dafür, das ich mit 11 jahren die Technologie erfunden habe. Der Anfang des Projekts war in mitte bis ende 2024, und die neuste version 2.1.1 wurde am 13.Juli.2025 Entwickelt.
