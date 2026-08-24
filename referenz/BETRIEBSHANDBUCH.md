# Betriebshandbuch — wie die Strategie im Alltag läuft

Datum: 24.08.2026
Rohausgabe: `betriebsbild.txt`, alle Orders: `betriebsbild_orders_blind.csv`
Zahlen aus dem Blindfenster 2022–2026 (1.695 Tage), gegengeprüft an der
gesamten Binance-Historie 2017–2026 (3.293 Tage).

Dies ist zugleich die Spezifikation für den Papertrading-Bot.

---

## 1. Der Ablauf in vier Schritten

Einmal täglich, direkt nach 00:00 UTC. Für **jeden Markt getrennt**:

**Schritt 1 — Trendsignal.** Vier gleitende Durchschnitte über 50, 100, 150
und 200 Tage berechnen. Zählen, über wie vielen davon der gestrige
Schlusskurs liegt.

| über … von 4 SMAs | Signal |
|---|---|
| 0 | 0,00 |
| 1 | 0,25 |
| 2 | 0,50 |
| 3 | 0,75 |
| 4 | 1,00 |

**Schritt 2 — Volatilitäts-Skala.** Schwankungsbreite der letzten 60
Tagesrenditen berechnen, annualisieren. Dann:
`Skala = min(1 ; 60 % / gemessene Vola)`.
Bei ruhigem Markt (Vola ≤ 60 %) ist die Skala 1, es wird nichts gedrosselt.
Bei Vola 80 % ist sie 0,75, bei 120 % ist sie 0,50.

**Schritt 3 — Zielgewicht.**
`Zielgewicht = 50 % × Signal × Skala`
Die 50 % sind das Budget des Marktes (zwei Märkte, je die Hälfte). Ein
Markt kann also nie mehr als 50 % des Gesamtkapitals belegen, beide
zusammen nie mehr als 100 %. **Es entsteht nie Hebel.**

**Schritt 4 — Handeln oder nicht.** Nur wenn das Zielgewicht um **mehr als
5 Prozentpunkte** vom tatsächlich gehaltenen Gewicht abweicht, wird eine
Order geschickt — auf die Differenz. Sonst passiert nichts.

Was nicht investiert ist, liegt als USDT auf dem Konto und wird verzinst.

> Die beiden Märkte werden **völlig unabhängig** behandelt. BTC kann voll
> investiert sein, während ETH komplett draussen ist. Es gibt kein
> gemeinsames Signal und keine gemeinsame Order.

---

## 2. Wie viele Orders — die echten Zahlen

| | Blindfenster 2022–26 | gesamte Historie 2017–26 |
|---|---|---|
| Orders pro Monat, beide Märkte | **7,5** | 6,9 |
| davon BTC | 3,8 | 3,6 |
| davon ETH | 3,7 | 3,3 |
| Kauf-Anteil | 50 % | 50 % |

Verteilung über die 56 Monate des Blindfensters:

| Orders im Monat | 0 | 1–4 | 5–9 | 10–14 | 15–19 |
|---|---|---|---|---|---|
| Anzahl Monate | **9** | 7 | 23 | 12 | 3 |

**Median 8, Mittel 7,4, Maximum 19.** Neun von 56 Monaten hatten
**überhaupt keine Order**.

### An wie vielen Tagen passiert etwas

- Handelstage: **362 von 1.695 = 21 %**
- An **79 % aller Tage passiert gar nichts** — der Bot rechnet, vergleicht,
  stellt fest, dass die Abweichung unter 5 % liegt, und legt sich wieder hin.
- Von den Handelstagen: **85 % nur ein Markt**, **15 % beide gleichzeitig**.

**Antwort auf die Frage „tradet er beide Assets auf einmal?": Nein, meistens
nicht.** In fünf von sechs Fällen bewegt sich nur einer der beiden.

---

## 3. Ordergrösse

| | in % des Gesamtkapitals | bei 5.000 € |
|---|---|---|
| kleinste (= Totband) | 5,0 % | 250 € |
| Median | **12,1 %** | **607 €** |
| Mittel | 12,7 % | 635 € |
| grösste | 37,5 % | 1.875 € |

Der Median liegt bei ~12 %, weil das Signal in Vierteln springt: ein Schritt
von 0,75 auf 0,50 verschiebt 12,5 % des Kapitals (ein Viertel von 50 %).
Die grossen Orders (25–37,5 %) entstehen, wenn das Signal mehrere Stufen auf
einmal überspringt.

---

## 4. Wie die Gewichtung aussieht

| | BTC | ETH | zusammen |
|---|---|---|---|
| Mittleres Gewicht | 24,6 % | 20,7 % | **45,3 %** |
| Median | 25,0 % | 12,5 % | 39,9 % |
| Maximum | 50,0 % | 50,0 % | 100,0 % |
| komplett draussen an … der Tage | 34 % | 37 % | — |

**Im Schnitt sind nur rund 45 % des Kapitals investiert**, der Rest liegt
als USDT da. Das ist kein Fehler, sondern die Strategie: Der Filter ist die
meiste Zeit nur teilweise dabei, und genau daraus entsteht der halbierte
Drawdown.

Wie oft welcher Zustand vorkommt:

| Signal | 0,00 | 0,25 | 0,50 | 0,75 | 1,00 |
|---|---|---|---|---|---|
| BTC | 34 % | 13 % | 6 % | 11 % | 35 % |
| ETH | 37 % | 13 % | 10 % | 11 % | 28 % |

Also grob: ein Drittel voll drin, ein Drittel komplett draussen, ein Drittel
irgendwo dazwischen.

Die Volatilitäts-Skala greift bei BTC selten (an 77 % der Tage voll
investiert, stärkste Drosselung auf 0,70) und bei ETH häufig (nur an 38 %
der Tage voll, Median 0,91, stärkste Drosselung auf 0,50). Das ist genau
der beabsichtigte Effekt — ETH schwankt mehr und wird deshalb öfter
zurückgenommen.

---

## 5. Wie lange eine Position steht

| | Phasen | Median | längste | davon unter 6 Tagen |
|---|---|---|---|---|
| BTC | 40 | 4 Tage | **280 Tage** | 26 |
| ETH | 30 | 9 Tage | **257 Tage** | 12 |

Die Median-Zahl täuscht: Es gibt viele sehr kurze Phasen (Fehlsignale am
Rand des Trends) und wenige sehr lange. **Das Geld wird in den langen
Phasen verdient** — eine 280-Tage-Position ist ein kompletter Bullenmarkt.
Die kurzen sind der Preis dafür.

---

## 6. Zwei echte Monate zum Vergleich

### Ruhig — März 2024, null Orders

Beide Märkte durchgehend auf Signal 1,00. Ab dem 21. sinkt die Vola-Skala
leicht von 1,00 auf 0,95, das Zielgewicht fällt von 50,0 % auf 47,7 %.
Abweichung 2,3 Prozentpunkte — **unter dem Totband, also keine Order.**
Genau dafür ist das Totband da.

### Unruhig — Juni 2023, 19 Orders

```
Datum        BTC Ziel   ETH Ziel   Order
2023-06-02      25,0%      37,5%   BTC verkaufen 12,4 %
2023-06-03      37,5%      50,0%   BTC kaufen 12,4 %  |  ETH kaufen 12,0 %
2023-06-06      25,0%      37,5%   BTC verkaufen 12,1 %  |  ETH verkaufen 12,3 %
2023-06-07      37,5%      50,0%   BTC kaufen 11,8 %  |  ETH kaufen 12,1 %
2023-06-08      25,0%      37,5%   BTC verkaufen 12,2 %  |  ETH verkaufen 12,4 %
...
2023-06-21      50,0%      25,0%   BTC kaufen 23,8 %  |  ETH kaufen 12,4 %
2023-06-22      50,0%      50,0%   ETH kaufen 24,7 %
```

Das ist ein **Sägezahn**: Der Kurs pendelt um einen der vier Durchschnitte,
das Signal springt an drei aufeinanderfolgenden Tagen hin und her. Jeder
dieser Wechsel kostet Gebühr. Das ist die unangenehme Seite eines täglichen
Filters und lässt sich nicht wegoptimieren — der Versuch, seltener zu
handeln (wöchentlich), hat die Rendite im Blindtest halbiert (23,1 % → 11,2 %).

Über das ganze Jahr gerechnet fallen dabei **rund 1,1–1,6 % des Kapitals an
Kosten** an. Bei 15–25 % Rendite ist das verkraftbar, aber man muss es
sehen, wenn es im Kontoauszug steht.

---

## 7. Was der Bot können muss

| Anforderung | Detail |
|---|---|
| Datenquelle | Tages-Schlusskurse ETH und BTC, 00:00 UTC |
| Historie beim Start | mindestens 260 Tage (200 für den SMA + 60 für die Vola) |
| Laufzeit | einmal täglich, kurz nach 00:00 UTC |
| Orderart | Markt, Spot, nur Kauf/Verkauf gegen USDT |
| Positionsprüfung | tatsächlich gehaltene Menge lesen, nicht das gemerkte Ziel |
| Totband | 5 Prozentpunkte des Gesamtkapitals |
| Sicherung | keine Order, wenn Kursdaten fehlen oder älter als 24 h sind |
| Protokoll | jede Entscheidung mitschreiben, auch die „keine Order"-Tage |

Die letzte Zeile ist wichtig: Weil an **79 % der Tage nichts passiert**,
muss aus dem Protokoll hervorgehen, dass der Bot gelaufen ist und sich
bewusst gegen eine Order entschieden hat. Sonst ist ein abgestürzter Bot
von einem ruhigen Markt nicht zu unterscheiden.

---

## 8. Offen fürs Papertrading

1. Entspricht der Kurs wenige Sekunden nach 00:00 UTC dem Tagesschluss?
   (Im 24/7-Markt sollte er das, ist aber zu messen.)
2. Wie gross ist die reale Slippage bei Orders von 250–1.875 €?
   Angenommen sind 0,02 %.
3. Verhält sich die Positionsabfrage der Börse so, wie der Bot es erwartet
   (Teilausführungen, Rundung der Mindestmenge)?
