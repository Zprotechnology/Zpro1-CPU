Bei Fragen, Problemen oder Kommentare bitte unter Issues oder bei Kommentare. Danke.

🔷 Zpro1 v2.1.1– Moderne Mobile CPU-Architektur (2025)

Zpro1 v2 ist eine hochmoderne, realistisch umsetzbare CPU-Architektur für Smartphones und mobile Geräte, die Leistung, Effizienz und Systemintelligenz optimal verbindet. Sie basiert auf 12 physischen Kernen, innovativen Software-Fusionstechniken und einer tief integrierten NPU-Steuerung.


---

1. Kernstruktur

12 reale Kerne, in 4 Cluster aufgeteilt:

3x Performance (4,32 GHz)

3x Balanced (3,5 GHz)

3x Effizienz (2,5 GHz)

3x Energiespar (1,9 GHz)



---

2. Thread Duplicating and Fusion System (TDFS)
!!!Funktioniert vollständig im Kernel!!!

Jeder Kern kann sich dynamisch in 2 virtuelle Threads aufteilen.

Threads bearbeiten unterschiedliche Aufgaben. TDFS aktiviert sich nur in fällen, wo viel paralelisierung gebraucht wird, oder viel Singelcore leistung gebraucht wird. dafür wird MCF verwendet.

Bietet SMT-ähnliche Vorteile, gesteuert von Gruppierung der leistung. bei niedrige paralelisierungs/leistungsintensive zb apps wird es minimal bis garnicht aktiviert. bei mittleren wird es im mittleren modus aktiv, und bei hohen in hohen logisch gesehen.

TDFS darf nur Kerne von grupierten Cluster nehmen, um instabilität zu vermeiden.

Ermöglicht fein abgestimmtes Multitasking mit minimalem Overhead.

Wie funktioniert die duplizierung der Threads?
Der Kernel wird so angepasst, das sich ein Thread verdoppeln kann, indem es virtuell einen zweiten erstellt, der aber dann die hälfte leistung des eigentlichen Threads nimmt, das sie 50/50 leistung vom eigentlichen haben. daas geschieht, wenn viel paralelisierung gebraucht wird. siehe MCF für TDFS leistungssteigerung.



---

3. Multi-Core Fusion (MCF) – Softwarebasierte Thread-Bündelung

Nimmt 2 bis 4 virtuelle Threads aus TDFS und gruppiert sie als einen logischen „Superkern“. Die threads Arbeiten dann zusammen, und werden eine anforderung gegeben, hoch in taktraten zu arbeiten, weil sich MCF nur in fällen aktiviert, wo viel leistung gebraucht wird. dies kann passieren, indem die MCF kerne/threads eine höhere grenze der niedrigsten taktrate gestellt wird. Zb normale grenze 300mhz, mit mcf Kernen aber dann zb 1000. Das war ein Beispiel.

Das Betriebssystem sieht diese Gruppe als einen einzelnen Kern. Es täuscht das System nicht, sondern das System packt sie in einer einkern gruppe rein, wo sie als einen benutzt werden.

Erhöht die Single-Core-Leistung.

Realistisch und energieeffizient, ohne thermische Probleme.



---

4. Cache-System mit integriertem L1.5-Fusion-Cache

L1.5 -Fusion-Cache ist zwischen dem L1-L2 Cache-Bereich integriert.

Spezieller Cache-Bereich für schnelle Kommunikation bei Fusion.

L1 & L2 bleiben aktiv; MCF-Threads dürfen aber nur den L1.5-cache nutzen.

Verbessert Cache-Kohärenz und Zugriffszeiten.
!!!Diese Cache ist außerdem im Kernel!!!



---

5. Background App Thread (BAT)

TDFS ermöglicht parallele Verarbeitung von Vordergrund- und Hintergrundaufgaben auf demselben/mehreren Kern/en. Beispielsweise wenn man eine app nutzt, und im hintergrund eine läuft.
---

6. Fast Efficient Architecture (FEA)

Dynamische Abschaltung oder Drosselung von Kernen bei hoher Last (>8W).

Sanfte Lastanpassung ohne FPS-Drops.

Die CPU kann Temperaturen von einzelnen Kernen messen, und die leistungsverteilung anpassen. Wenn zum Beispiel ein primekern voll ausgelastet ist, kommt TDFS MCF ins spiel, um threads von zum Beispiel medium kernen zu fusionieren, um die ähnliche leistung zu gefährleisten, während der voll ausgelastete prime kern ausgeschaltet wird, bis er abgekühlt wurde.



---

7. Neural Processing Unit (NPU)

Zentrale Steuerungseinheit für:

Thread-Management (TDFS & MCF)

Leistungsanpassung (FEA)

Cache-Steuerung

App-Klassifizierung (hoch, mittel, niedrig)



---

8. Kühlsystem

Vapor chamber an der CPU und vapor chamber "straßen"" an die Ränder des Handys und von der cpu zu den rändern. 
Keine mechanischen Lüfter, ideal für Smartphones.



---

9. GPU- Steuerungs System:

Rendering-Reihenfolge:

1. Auflösung


2. Lichtberechnung


3. Farbverarbeitung



Fließende Grafikleistung auch bei anspruchsvollen Spielen.






Wie kann man es ins Kernel einfügen? 
Spezieller code wird zuerst für TDFS geschrieben, und ist dann eine erweiterung der thread steuerung. Die thread steuerung davor wird dann auch optimiert für TDFS, damit sie optimal zusammen arbeiten können.
---

11. Zusammenfassung und Vorteile

Innovativ: Softwarebasierte Fusion (MCF) und dynamisches Thread-Management (TDFS).

Effizient: Intelligente Steuerung durch NPU und FEA.

Leistungsstark: Hohe Single- und Multi-Core-Leistung.

Flexibel: Paralleles Multitasking mit BAT.

Realisierbar: Vollständig umsetzbar mit aktueller 2025-Technologie.
Entwickelt von mir mit 11 jahren.
Diese Entwicklungsnotiz ist auch ein beweis dafür, das ich mit 11 jahren die Technelogie erfunden habe. Der Anfang des Projekts war in mitte bis ende 2024, und die neuste version 2.1.1 wurde am 13.Juli.2025 Entwickelt.
