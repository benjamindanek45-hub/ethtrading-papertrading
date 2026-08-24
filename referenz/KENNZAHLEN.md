# Kennzahlen auf einen Blick

Stand 24.08.2026 · Endkonfiguration · Belege in `AUSWERTUNG_03.md`,
`ENTSCHEIDUNGEN.md`, `gewichtung.txt`, `abschluss.txt`

---

## Die Strategie

| | |
|---|---|
| Markt | **BTC**, Spot, kein Hebel, keine Shorts |
| Signal | Anteil der SMA 50/100/150/200 über dem Vortagesschluss |
| Position | Signal × min(1 ; 60 % / Vola60), höchstens 100 % |
| Handelszeit | täglich 00:02 UTC, nur bei Abweichung > 5 Punkten |
| Freies Kapital | als USDT verzinst |

---

## Was gemessen wurde

| Fenster | Strategie | Buy & Hold | MaxDD Strategie |
|---|---|---|---|
| **2017–19** *(unabhängig)* | **+23,3 % p. a.** | +24,4 % | 28,8 % |
| 2020–21 *(Kalibrierung)* | +91,7 % p. a. | +281,5 % | — |
| **2022–26** *(blind)* | **+27,8 % p. a.** | +10,9 % | 26,8 % |
| 2012–26 BTC gesamt | +85,4 % p. a. | +93,2 % | 43,6 % |

Validiert über 14,6 Jahre, zwei Börsen, fünf Fenster.
Nulltest gegen Zufall: Perzentil 92–95.

---

## Was du erwarten darfst

### Die Planungszahl: **12–20 % p. a.**

Gemessen wurden **23–28 %** in den beiden unabhängigen Fenstern. Ich setze
die Erwartung bewusst **darunter**, aus drei Gründen:

1. **Die Vergangenheit war aussergewöhnlich.** BTC hat in 14 Jahren das
   Tausendfache gemacht. Eine Long-or-flat-Strategie erbt die Rendite des
   Basiswerts — wenn BTC künftig 15 % statt 40 % p. a. macht, fällt die
   Strategie mit.
2. **Der Vorteil liegt im Drawdown, nicht in der Rendite.** Über alle
   Fenster schlägt die Strategie Buy-and-Hold bei der Rendite nur
   manchmal — beim Drawdown **immer**. Das ist der übertragbare Teil.
3. **Reserve für Ausführung.** Die echte Slippage misst erst das
   Papertrading. Liegt sie über 0,02 %, kostet das Rendite.

### Die Bandbreite eines einzelnen Jahres

Aus allen rollenden Ein-Jahres-Fenstern (BTC 2012–2026):

| | |
|---|---|
| schlechtestes Jahr | **−38,7 %** |
| unteres Fünftel | −20,5 % |
| Median | +81,3 % |
| bestes Jahr | +3.302 % *(2013er Blase)* |
| **Anteil negativer Jahre** | **22,1 %** |

**Ein Jahr von vier ist negativ.** Ab drei Jahren Haltedauer war in
14,6 Jahren kein einziges rollendes Fenster negativ (schlechteste
+13,8 % p. a.).

### Längste Verlustphase: **31 Monate**

Zweieinhalb Jahre unter dem alten Höchststand, ohne dass etwas kaputt ist.
Das ist die Zahl, die in der Praxis am meisten weh tut.

---

## In Euro, bei 12–20 % p. a.

| Einsatz | Gewinn/Jahr | nach 3 Jahren | nach 5 Jahren | schlechtestes Jahr |
|---|---|---|---|---|
| 1.000 € | 120–200 € | 1.405–1.728 € | 1.762–2.488 € | −387 € |
| 5.000 € | 600–1.000 € | 7.025–8.640 € | 8.812–12.442 € | −1.935 € |
| 10.000 € | 1.200–2.000 € | 14.049–17.280 € | 17.623–24.883 € | −3.870 € |
| 25.000 € | 3.000–5.000 € | 35.123–43.200 € | 44.058–62.208 € | −9.675 € |

Mindesteinsatz **1.000 €** (darunter werden die Order-Stufen zu klein),
unproblematisch ab **2.500 €**.

Mit Compounding — Gewinne bleiben im Depot. Der Drawdown wächst dabei mit:
30 % Verlust nach drei guten Jahren kosten absolut ein Vielfaches von 30 %
am Anfang.

---

## Betrieb

| | |
|---|---|
| Orders | **3–4 pro Monat** |
| Tage ohne jede Aktivität | **79 %** |
| Ordergrösse | Median 12 % des Kapitals |
| Mittlerer investierter Anteil | 45 % — der Rest liegt als USDT |
| Handelskosten | 1,1–1,6 % des Kapitals pro Jahr |
| Kostenempfindlichkeit | gering: bei **8-facher** Gebühr noch +30,5 % statt +41,8 % |

---

## Die drei unbequemen Zahlen

1. **22 % der Jahre sind negativ**, das schlechteste mit −38,7 %.
2. **Bis zu 31 Monate unter Wasser** — ohne dass die Strategie versagt.
3. **Du bist im Schnitt nur zu 45 % investiert.** Wenn der Markt steigt und
   die Hälfte deines Geldes als USDT herumliegt, fühlt sich das falsch an.
   Genau daraus kommt der halbierte Drawdown. Wer dann nachlegt, hat die
   Strategie abgeschaltet.
