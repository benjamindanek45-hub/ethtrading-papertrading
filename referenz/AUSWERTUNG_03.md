# Auswertung 03 — Verbesserungssuche abgeschlossen

Datum: 24.08.2026
Rohausgaben: `labor.txt`, `labor2.txt`, `validierung_final.txt`,
`abschluss.txt`; CSV: `labor.csv`, `labor_urteil.csv`, `labor2.csv`,
`validierung_final.csv`, `rollende_fenster.csv`

---

## Ergebnis in einem Satz

Von neun geprüften Ideenfamilien hat genau **eine** die Blindtests
überstanden — die Volatilitäts-Skalierung. Sie ist in **5 von 5 Fenstern**
besser bei Drawdown und Rendite je Risiko, geprüft über 14,6 Jahre auf zwei
Börsen. **Ich empfehle, die Suche hier zu beenden** — nicht aus Erschöpfung,
sondern weil jede weitere getestete Variante die Wahrscheinlichkeit erhöht,
dass ein scheinbarer Gewinner nur Rauschen ist.

---

## 1. Was getestet wurde und was durchgefallen ist

| Idee | Blindergebnis | Urteil |
|---|---|---|
| **Volatilitäts-Skalierung** | besser in 5/5 Fenstern bei Calmar und Drawdown | **behalten** |
| Zins auf nicht investiertes Kapital | +1,5 bis 2 Punkte p. a., risikofrei | **behalten** |
| Totband 2–10 % gegen Kleinstumschichtungen | leicht besser, weniger Kosten | **behalten** |
| Breites Universum (4 / 8 / 22 Märkte) | 23,1 % → 17,2 % → 8,7 % → 2,2 % p. a. | verworfen |
| Querschnitts-Momentum (Top 3/5/8) | Training +643 % p. a., blind **−16,5 %** | verworfen |
| Inverse-Vola-Gewichtung über Märkte | blind schwächer als gleich gewichtet | verworfen |
| Wöchentliches statt tägliches Rebalancing | 23,1 % → 11,2 % p. a. | verworfen |
| EMA statt SMA | blind 17,2 % statt 23,1 % | verworfen |
| Einzelner SMA200 statt Ensemble | 2017–19 −8,8 % statt +3,9 % | verworfen |
| Ausbruchs-Filter statt SMA | blind 16,2 % statt 23,1 % | verworfen |

Dazu die Fehlschläge der Runden 1 und 2, die weiterhin gelten: Intraday-
Sweeps, Session-Muster, Tagesreversion, Kalendermuster, Donchian-Ausbruch,
Funding-Signal und **jede Form von Short**.

### Der lehrreichste Fehlschlag

**Querschnitts-Momentum** sah im Trainingsfenster spektakulär aus:
+643 % p. a., Sharpe 2,26. Blind: **−16,5 % p. a. bei 75 % Drawdown.**
Das Trainingsfenster 2020–21 *war* die Altcoin-Saison; die Regel „kaufe,
was zuletzt am stärksten gestiegen ist" hat dort einfach die Saison
abgebildet. Das ist die sauberste Demonstration von Überanpassung in diesem
ganzen Projekt — und der Grund, warum jede Zahl aus dem Trainingsfenster
wertlos ist, solange sie nicht blind bestätigt wurde.

### Warum mehr Märkte schaden

Der Rückgang von 23,1 % (2 Märkte) auf 2,2 % (22 Märkte) ist monoton und in
beiden Prüfstand-Durchgängen gleich. Der Grund ist einfach: Der Trendfilter
verhindert keine Verluste, er verkürzt sie. Ein Coin, der 95 % verliert und
sich nie erholt, produziert dabei laufend Fehlsignale — der Filter kauft
jeden Zwischenanstieg und verkauft jeden Rückfall. Bei ETH und BTC gibt es
nach jedem Bärenmarkt ein neues Hoch; bei IOTA, ALGO oder NEO nicht.

---

## 2. Was behalten wurde: Volatilitäts-Skalierung

Die Position wird zusätzlich mit `Zielvola / gemessene Vola` skaliert,
gedeckelt bei 1 — in ruhigen Phasen also voll investiert, in wilden
reduziert. Es entsteht nie Hebel, alles bleibt auf Spot handelbar.

Robustheit über das ganze Raster (ETH+BTC, blind 2022–26): **alle 15**
Kombinationen aus Zielvola (30–80 %) und Messfenster (20/30/60 Tage)
verbessern das Fenster 2017–19 und senken den Drawdown. Das 60-Tage-Fenster
ist bei jeder Zielvola das beste — ein Muster, kein Glückstreffer.

Gewählt: **Zielvola 60 %, Messfenster 60 Tage.**

---

## 3. Abschlussvalidierung auf vier Fenstern

Neu hinzugekommen: **Bitstamp-BTC ab 2012** — andere Börse, kompletter
Extra-Zyklus mit der 2013er Blase und dem Bärenmarkt 2014/15 (−85 %).
Diesen Zeitraum hat weder Binance noch irgendein Test dieses Projekts je
gesehen. Datenabgleich auf 3.294 gemeinsamen Tagen: mittlere Abweichung zu
Binance 0,19 %.

| Fenster | Buy & Hold | ALT (ohne Skalierung) | **NEU (mit Skalierung)** |
|---|---|---|---|
| **BITSTAMP 2012–17** | +233,3 % / DD 84,9 % | +190,4 % / DD 69,6 % | **+160,8 % / DD 39,9 %** |
| BITSTAMP 2012–26 | +93,2 % / DD 84,9 % | +93,7 % / DD 69,6 % | **+85,4 % / DD 43,6 %** |
| VOR 2017–19 | −1,6 % / DD 88,0 % | +3,9 % / DD 39,7 % | **+15,4 % / DD 29,3 %** |
| TRAIN 2020–21 *(bekannt)* | +281,5 % / DD 58,0 % | +162,3 % / DD 40,8 % | **+136,1 % / DD 30,9 %** |
| **BLIND 2022–26** | +1,9 % / DD 68,4 % | +23,1 % / DD 29,6 % | **+26,1 % / DD 28,3 %** |

**NEU besser bei Drawdown: 5 von 5. Bei Rendite je Risiko: 5 von 5.**
Bei der reinen Rendite nur 2 von 5 — und das ist korrekt so: In
Aufwärts-Explosionen (2013, 2020/21) gibt die Skalierung Oberseite ab, um
den Drawdown zu halbieren. Genau dafür ist sie da.

**Nulltest** gegen 400 zufällig verschobene Gewichtsreihen mit gleicher
Zeit im Markt: Perzentil 91,8 (Bitstamp 2012–17), 72,8 (2017–19),
95,2 (blind 2022–26).

---

## 4. Was realistisch zu erwarten ist

Nicht der Mittelwert eines günstigen Fensters, sondern die Verteilung über
**alle** möglichen Startzeitpunkte:

**ETH+BTC 2017–2026 (9,0 Jahre), annualisiert:**

| Haltedauer | schlechteste | 5 % | Median | 95 % | Anteil negativ |
|---|---|---|---|---|---|
| 1 Jahr | **−37,8 %** | −18,7 % | +32,5 % | +352,7 % | **23,1 %** |
| 2 Jahre | −7,2 % | +4,0 % | +46,0 % | +158,7 % | 2,9 % |
| 3 Jahre | +14,2 % | +22,8 % | +52,4 % | +112,4 % | **0 %** |
| 5 Jahre | +16,8 % | +21,5 % | +57,4 % | +74,9 % | **0 %** |

**BTC 2012–2026 (14,6 Jahre)** liefert praktisch dasselbe Bild:
1 Jahr 22,1 % negativ (schlechteste −38,7 %), ab 3 Jahren 0 % negativ
(schlechteste +13,8 %).

**Längste Verlustphase** (unter dem alten Hoch): **24 Monate** bei ETH+BTC,
**31 Monate** bei BTC. Das ist die Zahl, die in der Praxis am meisten weh
tut — zwei Jahre lang unter Wasser, ohne dass die Strategie kaputt ist.

**Kostenempfindlichkeit** — falls die Ausführung schlechter läuft als
angenommen:

| Kosten je Umschichtung | ETH+BTC 2017–26 |
|---|---|
| 0,12 % (angenommen) | +41,8 % p. a. |
| 0,25 % (doppelt) | +40,1 % p. a. |
| 0,50 % (vierfach) | +36,8 % p. a. |
| 1,00 % (achtfach) | +30,5 % p. a. |

Selbst bei achtfachen Kosten bleibt die Strategie intakt. Bei rund
9,5 Umschichtungen des Kapitals pro Jahr ist sie schlicht nicht
kostenempfindlich — der genaue Gegensatz zu Runde 1.

> **Wichtige Einordnung:** Die Mediane von 32–86 % p. a. stammen aus einer
> Anlageklasse, die in 14 Jahren das Tausendfache gemacht hat. Sie sind
> **nicht** als Prognose zu lesen. Übertragbar ist das *relative* Ergebnis:
> halber Drawdown bei ähnlicher oder besserer Rendite als Halten. Für die
> Planung bleibt die Zahl aus Auswertung 02 stehen: **15–25 % p. a.**, und
> davon geht ein Jahr von vier ins Minus.

---

## 5. Korrekturen an früheren Zahlen

Drei Fehler sind in diesem Durchgang aufgefallen und behoben:

1. **Warmlauf-Artefakt (wichtig).** Die alte Ensemble-Implementierung liess
   noch nicht berechenbare SMAs mit „flat" *abstimmen*, statt sich zu
   enthalten. In den ersten 200 Tagen einer Kursreihe war das kein Ensemble,
   sondern eine willkürliche Gewichtung — und sie hat die Rally Ende 2017
   (+173 %) zufällig richtig mitgenommen. Neue Regel: ein Markt ist erst
   handelbar, wenn alle vier SMAs existieren. Dadurch fällt das Fenster
   2017–19 für ETH+BTC von +18,3 % auf **+3,9 % p. a.** (ohne Skalierung).
   Die früher genannte Aussage „das Portfolio rettet 2017–19" war zum Teil
   dieses Artefakt.
2. **Funding annualisiert** war um den Faktor 3 zu hoch angegeben
   (die Tagessumme wurde nochmals mit 3 multipliziert). Richtig: **8,3 % p. a.**
   bei voller Position, nicht 25 %. Der Gesamteffekt auf die Rendite
   (~4,6 Punkte p. a.) war davon nicht betroffen und bleibt gültig.
3. **Kostenspalte** in der Sensitivitätstabelle war auf die Basisannahme
   fest verdrahtet. Behoben; die Renditen waren nie betroffen.

### Was die saubere Rechnung zur Asset-Frage sagt

| | 2017–19 | 2020–21 | 2022–26 |
|---|---|---|---|
| nur ETH | −5,8 % | +190,0 % | +18,5 % |
| **nur BTC** | **+12,8 %** | +112,4 % | **+25,8 %** |
| ETH+BTC 50/50 | +3,9 % | +162,3 % | +23,1 % |

**BTC war in beiden unabhängigen Fenstern besser als das Portfolio** — mit
weniger Drawdown. ETH gewinnt nur im Trainingsfenster, also in der
Altcoin-Saison. Der Diversifikationsgewinn ist bei einer Korrelation von
0,65–0,71 klein: ETH ist im Wesentlichen ein hebelnderes BTC.

Ich lasse ETH+BTC 50/50 trotzdem stehen, weil eine Entscheidung nach zwei
Fenstern dünn ist und der Auftrag von ETH ausging. **Wer die Rendite
maximieren will, nimmt nach dieser Datenlage BTC allein oder eine
BTC-Übergewichtung (z. B. 70/30).** Das ist eine Risiko-Entscheidung, keine
Rechenaufgabe.

---

## 6. Warum ich die Suche hier beenden würde

Nicht, weil nichts mehr denkbar wäre, sondern aus einem konkreten Grund:

Inzwischen sind rund **60 Konfigurationen** blind ausgewertet. Bei 60 Tests
ist zu erwarten, dass allein durch Zufall etwa drei davon auf dem
5-%-Niveau „signifikant" aussehen. Jede weitere getestete Variante senkt
also den Informationswert eines neuen Fundes — irgendwann findet man
garantiert etwas, und garantiert ist es dann Rauschen. Genau das hat
Querschnitts-Momentum im Trainingsfenster vorgeführt.

Was dafür spricht, dass der gefundene Effekt echt ist:
- Er hält über **14,6 Jahre, zwei Börsen, drei Anlagen und fünf Fenster.**
- Er hält bei **vier verschiedenen Trenddefinitionen** (SMA-Ensemble, EMA,
  einzelner SMA200, Ausbruchsfilter) — alle blind zwischen +16 und +26 %.
- Er hält über **alle 16 Parameterkombinationen** des Filters.
- Er ist die am besten dokumentierte Anomalie der Finanzliteratur
  (Zeitreihen-Momentum). Dass ausgerechnet sie überlebt und die
  selbstgefundenen Muster nicht, ist das erwartete Ergebnis.

Nicht geprüft — und ehrlich gesagt ausserhalb dessen, was hier sinnvoll
geht: Optionen, On-Chain-Daten, Orderbuch-Mikrostruktur. Das sind eigene
Projekte mit eigener Infrastruktur, keine Variante dieser Strategie.

---

## 7. Endgültige Konfiguration für das Papertrading

| | |
|---|---|
| **Märkte** | ETH und BTC, je 50 % (BTC-Übergewichtung ist vertretbar) |
| **Instrument** | Spot |
| **Hebel** | keiner |
| **Signal** | Anteil der SMAs 50/100/150/200, über denen der Schlusskurs liegt |
| **Positionsgrösse** | Signal × min(1 ; 60 % / Vola60) je Markt, Brutto ≤ 100 % |
| **Rebalancing** | täglich um 00:00 UTC, nur wenn Abweichung > 5 % |
| **Freies Kapital** | als USDT verzinsen (2–4 % p. a.) |
| **Erwartung** | 15–25 % p. a., Drawdown bis 45 %, bis 24 Monate unter Wasser |
| **Ein Jahr von vier** | negativ, schlechtestes Jahr −20 bis −38 % |

Nächster Schritt: Papertrading auf dem GoldBotServer. Zu klären ist dort
nur noch die Ausführung — ob der Kurs wenige Sekunden nach 00:00 UTC
tatsächlich dem Tagesschluss entspricht und wie gross die reale Slippage
bei dieser Ordergrösse ist.

---

## 8. Nachtrag: „Daily-Capitulation, +114 USDT/Monat" aus der Vorarbeit

Rohausgabe: `nachtest_kapitulation.txt`, CSV: `nachtest_kapitulation.csv`

`00_Kontext/BESTANDSAUFNAHME.md` führt aus der Vorarbeit einen Kandidaten,
der nie nach den Massstäben dieses Projekts geprüft wurde:

> `Daily-Capitulation (8 Alts gepoolt) | PF 3.215, Val +114 USDT/Mo`
> `— aber Worst-Month −25.6 %, nicht deployt`

Nachgebaut wie beschrieben (Einbruch ≥ X % binnen 3 Tagen → long für H Tage,
gleichgewichtet gepoolt), 4 Schwellen × 3 Haltedauern × 3 Universen = 36
Varianten, vier Zeitfenster:

| Universum | blind 2022–26 positiv | Median blind | beste blind |
|---|---|---|---|
| nur Alts (20) *— die Vorarbeits-Variante* | **0 von 12** | −22,6 % p. a. | −6,9 % |
| alle 22 | **0 von 12** | −22,4 % p. a. | −5,3 % |
| nur ETH+BTC | 1 von 12 | −7,8 % p. a. | **+0,8 %** |

**1 von 36 Varianten ist blind positiv, und zwar mit +0,8 % p. a.**
Die Drawdowns der Alt-Variante reichen bis **97 %**.

Drei Dinge fallen dabei auf:

1. **Auch im eigenen Fenster der Vorarbeit (2023–26)** sind nur 3 bis 5 von
   12 Varianten positiv, die beste mit +9,0 % p. a. Die +114 USDT/Monat
   lassen sich also nicht einmal dort reproduzieren.
2. **Die Kosten sind der wahrscheinliche blinde Fleck.** Wer jeden
   −6-%-Dreitagesrückgang über 20 Alts kauft, schichtet so viel um, dass
   allein die Gebühren **13 bis 25 % des Kapitals pro Jahr** auffressen.
   Das ist exakt die Kostenfalle aus Auswertung 01, nur eine Ebene höher.
3. **„+114 USDT/Monat" ist keine Rendite**, solange die Kapitalbasis nicht
   dabeisteht. Bei 1.000 USDT wären das +137 % p. a., bei 10.000 USDT
   +13,7 %. Ohne diese Angabe ist die Zahl nicht vergleichbar.

Damit ist es die **dritte unabhängige Bestätigung** derselben Sache: D2
(Kapitulation-Long auf ETH) endete blind im Ruin, der Musterscan zeigte die
ganze Familie „Mehrtagesbewegung" blind mit negativem Excess, und jetzt
scheitert auch die gepoolte Alt-Fassung. Die Vorarbeit hatte sie mit
Verweis auf den −25,6-%-Monat ohnehin nicht ausgerollt — nach diesen Zahlen
war das die richtige Entscheidung, wenn auch aus dem falschen Grund.
