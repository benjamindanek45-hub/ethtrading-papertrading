# Papertrading — Ziel, Kriterien, Dauer

Aufgesetzt: 24.08.2026, erster Lauf 25.08.2026 00:02 UTC
Server: GoldBotServer (204.168.228.46), `/root/ethtrading/06_Papertrading/`

---

## 1. Die Gewichtung: 100 % BTC

Ausgerechnet in `04_Backtests/gewichtung.py`, BTC-Anteil in Zehnerschritten
von 0 bis 100 %, bewertet auf den beiden **unabhängigen** Fenstern:

| BTC/ETH | VOR 2017–19 | BLIND 2022–26 | Mittel | schlechtestes | Calmar |
|---|---|---|---|---|---|
| 0/100 | +6,2 % | +23,2 % | 14,7 | 6,2 | 0,46 |
| 30/70 | +13,5 % | +24,9 % | 19,2 | 13,5 | 0,66 |
| 50/50 | +15,4 % | +26,1 % | 20,8 | 15,4 | 0,73 |
| 80/20 | +21,0 % | +26,2 % | 23,6 | 21,0 | 0,82 |
| **100/0** | **+23,3 %** | **+27,8 %** | **25,5** | **23,3** | **0,93** |

Der Zusammenhang ist **monoton**: Mehr BTC ist in jeder Kennzahl und in
beiden Fenstern besser — Rendite, schlechtestes Fenster, Drawdown und
Rendite je Risiko. Es gibt kein Zwischenoptimum, das man treffen müsste.
Das ist ein deutlich stärkerer Befund als „BTC hat zweimal gewonnen".

Zur Kontrolle: Das Trainingsfenster hätte **100 % ETH** empfohlen
(+162,6 % p. a.) — also exakt das Gegenteil. Noch ein Beleg dafür, dass
Zahlen aus dem Kalibrierungsfenster wertlos sind.

**Warum BTC gewinnt**, mechanisch: ETH schwankt stärker bei gleicher
Trendstruktur. Die Volatilitäts-Skala drosselt ETH deshalb dauernd — ETH ist
nur an 38 % der Tage voll investiert, BTC an 77 %. ETH liefert also weniger
Marktexposure bei gleichen Sägezahn-Kosten.

### Was diese Entscheidung kostet

Ein Backtest kennt kein Einzelrisiko. Alles auf einen Wert zu setzen heisst,
dass ein Ereignis, das nur BTC trifft, voll durchschlägt. Das steht in
keiner der Zahlen oben. Wer das nicht will, nimmt 80/20 und gibt dafür
rund **2 Prozentpunkte pro Jahr** ab — das ist der ehrliche Preis der
Streuung.

Deshalb läuft im Papertrading **beides parallel**:

| Depot | Aufteilung | Zweck |
|---|---|---|
| `live` | BTC 100 % | Kandidat für den Echtgeldbetrieb |
| `schatten` | BTC 50 / ETH 50 | sammelt weiter Belege gegen ETH |

Je 10.000 EUR fiktives Startkapital.

---

## 2. Was das Papertrading NICHT leisten kann

**Es kann nicht zeigen, dass die Strategie Geld verdient.** Aus den
rollenden Fenstern (Auswertung 03): 23 % aller Ein-Jahres-Zeiträume sind
negativ, das schlechteste mit −38 %. Ein gutes oder schlechtes Quartal sagt
deshalb nichts über die Strategie — nur über das Wetter.

Wer nach drei guten Monaten Papertrading die Einsatzgrösse erhöht, hat aus
Rauschen gelernt. Wer nach drei schlechten Monaten abbricht, ebenfalls.

---

## 3. Was es leisten SOLL — vier mechanische Fragen

Bei diesen konvergieren schon wenige Wochen zu einer belastbaren Antwort:

### 3.1 Läuft der Bot zuverlässig?
Erwartung: **100 % der Tage, keine Ausnahme.** Ein ausgefallener Tag ist
live ein verpasstes Signal. Weil an 79 % der Tage keine Order fällt, wird
**jeder** Lauf protokolliert — auch die ohne Order. Sonst ist ein
abgestürzter Bot von einem ruhigen Markt nicht zu unterscheiden.

### 3.2 Stimmen die Signale mit dem Backtest überein?
Der Bot rechnet dieselben vier SMAs und dieselbe 60-Tage-Vola wie der
Backtest, nur mit Live-Daten. Jede Abweichung ist ein Implementierungsfehler
und muss vor Echtgeld gefunden werden.

### 3.3 Wie gross ist die echte Slippage?

> **24.11.2026: Diese Regel war falsch spezifiziert und ist ersetzt.**
> Ursprünglicher Wortlaut: *„Der Bot misst bei jedem Lauf, wie weit der Kurs
> um 00:02 UTC vom Schlusskurs um 00:00 UTC abweicht. Liegt das dauerhaft
> über 0,04 %, muss die Renditeerwartung nach unten korrigiert werden.“*
>
> **Warum falsch** (Prüfbericht `review/2026-11-24.md`, Abschnitt 2C):
> 1. Gemessen wird Kursdrift durch zwei Minuten Zeitversatz, keine
>    Ausführung. Die 0,02 % des Backtests stehen für Spread und Marktwirkung —
>    beides kann Papier nicht messen.
> 2. Die Drift hat Mittelwert null (92 Tage: −0,009 %, Streuung 0,075 %).
>    Der Median ihres **Betrags** liegt bei Mittelwert null rechnerisch bei
>    ≈ 0,05 % — die Schwelle 0,04 % reißt damit fast immer, auch an Tagen
>    ohne Order (0,045 %). Drift zu unseren Gunsten ist kein Kostenposten.
> 3. An den 4 Order-Tagen lag die Drift vorzeichenrichtig bei Median
>    −0,027 % (3 von 4 zu unseren Gunsten); n = 4 trägt ohnehin keinen Median.
> 4. Selbst wenn alles Kosten wären: 12,45-facher Jahresumschlag × 0,038 %
>    = 0,47 % im Jahr = 4,70 € auf 1.000 €.
>
> **Urteil für Phase 1:** nach Wortlaut nicht bestanden, nach Zweck erfüllt —
> kein Hinweis auf Kosten über der Annahme.

**Ersatzregel ab Phase 2 (echte Orders, `livetrader.py` → `logs/ausfuehrung.csv`):**

| Größe | Definition | Grenze |
|---|---|---|
| **Ausführungskosten** | (Fülldurchschnitt − Mitte beim Absenden) / Mitte, vorzeichenrichtig (Kauf teurer bzw. Verkauf billiger = positiv), Gebühr getrennt | **Median ≤ 0,10 % je Seite** über alle Orders der Phase |
| Einzelorder | dieselbe Größe | **> 1 %** = Fehlersuche (Größe, dünnes Buch, Rundung) |
| Zeitdrift | Mitte beim Absenden gegen Schluss 00:00, vorzeichenrichtig | nur protokollieren, **keine** Schwelle; ausgewertet wird allein ihr Mittelwert |

### 3.4 Handelt er so oft wie erwartet?
Erwartung aus dem Betriebshandbuch: **3–4 Orders im Monat** für `live`
(BTC allein), **7–8** für `schatten`. Deutlich mehr hiesse, dass das Totband
nicht greift; deutlich weniger, dass das Signal klemmt.

---

## 4. Wie lange — und wie es weitergeht

| Phase | Dauer | Einsatz | Voraussetzung zum Weitergehen |
|---|---|---|---|
| **1 Papertrading** | **60–90 Tage** | 0 EUR | alle vier Punkte aus Abschnitt 3 erfüllt |
| **2 Echtgeld klein** | 60–90 Tage | 20–25 % des Zielbetrags | Ausführung stimmt mit dem Papertrading überein |
| **3 Echtgeld voll** | dauerhaft | Zielbetrag | Phase 2 ohne Zwischenfall |

**Warum 60 Tage das Minimum sind:** Bei 3–4 Orders im Monat sammelt `live`
in 60 Tagen etwa **7–8 Orders**. Das reicht, um die Slippage grob zu
schätzen. Weniger Zeit heisst zu wenige Orders für eine belastbare Zahl.

**Warum 90 Tage besser sind:** Dann liegt die Handelshäufigkeit über einen
Zeitraum vor, der mit den 7,5 Orders/Monat aus dem Backtest sinnvoll
vergleichbar ist, und der Bot hat mindestens einen Monatswechsel,
einen Feiertagszeitraum und wahrscheinlich eine Sägezahn-Phase gesehen.

**Frühester realistischer Echtgeldstart: Ende Oktober 2026** (60 Tage),
**empfohlen Ende November 2026** (90 Tage).

### Abbruchgründe — dann NICHT live gehen

- Ein einziger unerklärter Ausfalltag, der nicht behoben wurde
- ~~Slippage dauerhaft über 0,04 % im Median~~ (falsch spezifiziert, siehe
  3.3) → ab Phase 2: Ausführungskosten im Median über 0,10 % je Seite
- Signalabweichung zum Backtest, egal wie klein
- Mehr als doppelt so viele Orders wie erwartet

Nicht auf der Liste: eine negative Rendite im Papertrading. Die ist
statistisch bedeutungslos und **kein** Abbruchgrund.

### Ergebnis Phase 1 (Prüfbericht 24.11.2026)

92 von 92 Läufen; der unveränderte Backtest-Code rechnet über
25.08.–24.11.2026 an allen 92 Tagen dasselbe Signal und dieselbe Skala und
erzeugt dieselben 4 (live) bzw. 10 (schatten) Orders. 3.1, 3.2 und 3.4
erfüllt (nur 4 Orders, weil der Backtest über genau diese Tage auch nur 4
erzeugt), 3.3 siehe oben. Technisch reif für Phase 2.

> **Entscheidung Ben, 24.11.2026: P1 geht NICHT live.** P1 bringt bei
> kleinem Einsatz laut Rücktest nur ein paar Euro im Monat und ist ein
> Risikoregler, kein Ertragsbringer (Runde 77). P1 bleibt Papertrading und
> läuft unverändert weiter. `livetrader.py` bleibt im Repo, ist aber nicht
> aktiv: `live_erlaubt` bleibt `false`, `ethlive.timer` ist nicht
> eingerichtet, es gibt keinen P1-Schlüssel. Die Abschnitte unten
> beschreiben, wie Phase 2 liefe, falls Ben das je neu entscheidet.

### Phase 2 — wie sie liefe (gebaut, nicht aktiv)

| | |
|---|---|
| Bot | `livetrader.py`, importiert `signal_und_skala()` aus `papertrader.py`, Timer `ethlive.timer` 00:03 UTC |
| Signaldaten | Binance-Tageskerzen 00:00 UTC (wie Papier und Backtest), **nicht** Bitgets `1D` |
| Ausführung | Bitget Spot BTCUSDT, Marktorder auf die Differenz, Totband 5 pp |
| Budget | fest `budget_usdt` in `config.json` (nur Server): 1.000 € in USDT. Der Bot führt sein **eigenes Teildepot** (eigenes USDT + eigenes BTC aus den echten Füllungen); Zielgewichte gelten für dieses Teildepot, **nie** für den Kontostand |
| Abgleich | Börsenbestand gegen Teildepot (fehlt etwas → keine Order, Telegram); Papertrader gegen Live (Signal, Skala, Ordertag, Gewicht > 2 pp → Telegram) |
| Zins auf USDT | weggelassen (≈ 15 €/Jahr, bräuchte ein zusätzliches Schlüsselrecht) |
| Sperren | `live_erlaubt` setzt nur Ben; Zustand aus dem Trockenbetrieb sperrt den ersten echten Lauf, bis `--zuruecksetzen`; ein Lauf je Tag; Daten älter als 24 h → keine Order |
| Testsperre | `tools/update_pruefen.py` prüft seit 24.11.2026 auch P1: Konstanten von papertrader/livetrader unverändert, Signal und Skala an 120 Tagen identisch |

**Bewertung nach 60–90 Tagen:** 100 % Läufe · jede Live-Order = Papier-Order
(Tag, Richtung, Ziel ± 1 pp), keine zusätzliche, keine fehlende ·
Ausführungskosten nach 3.3 · kein unerklärter Zwischenfall. Die Rendite ist
**kein** Kriterium.

### Kontoregel für alle Bots (Ben, 24.11.2026)

1. **Neue Bots kommen auf bestehende Konten** — es werden keine neuen
   Unterkonten angelegt.
2. Jeder Bot bekommt ein **festes Budget** und einen **eigenen API-Schlüssel**
   mit den kleinsten nötigen Rechten (nie Auszahlen, nie Transfer,
   IP-Bindung auf den Server).
3. **Hand-Trades auf demselben Markt sind auf einem Bot-Konto tabu.** Ein
   Konto mit Hand-Trades kommt für einen Bot nicht in Frage.
4. Ein Bot rechnet nie mit dem Kontostand, sondern nur mit seinem eigenen,
   selbst nachgeführten Bestand; der Kontostand dient dem Abgleich.

### Echtgeldregel für alle Strategien (Ben, 24.11.2026)

Echtgeld nur für Strategien, die sich laut Rücktest **bei Bens Kapital
spürbar lohnen** können. Bevor eine Strategie mit Echtgeld startet (oder
ihr Einsatz steigt), nennt Claude Ben zwei Zahlen **in Euro** beim
geplanten Einsatz:

1. **erwarteter Ertrag je Monat** (aus dem Rücktest, mit der Spanne, wenn
   die Jahre stark schwanken),
2. **schlechtestes erwartetes Jahr** (schlechtestes Kalenderjahr im
   Rücktest bzw. der größte Rückgang, je nachdem, was schlimmer ist).

Ben entscheidet dann, ob es sich lohnt. Ohne diese beiden Zahlen geht
nichts scharf. Rückwirkend angewandt auf P3 und P4 im Bericht
`review/2026-11-25.md`.

---

## 5. Betrieb

```
systemctl list-timers ethpaper.timer      # läuft der Timer?
journalctl -u ethpaper.service -n 40      # was hat er zuletzt getan?
cd /root/ethtrading/06_Papertrading
  .venv/bin/python papertrader.py --status  # Stand, ohne zu buchen
  .venv/bin/python bericht.py               # Auswertung + Go/No-Go-Liste
```

- Timer: täglich **00:02 UTC**, `Persistent=true` (holt nach, falls der
  Server aus war), 30 s Streuung.
- Der Dienst ist ein `oneshot`: startet, rechnet ~0,4 s, beendet sich.
  `Nice=15`, `CPUQuota=25%`, `MemoryMax=512M` — der Gold-Bot unter Wine
  hat auf derselben Maschine Vorrang und wird nicht berührt.
- `ProtectSystem=strict`, schreibbar ist nur das eigene Verzeichnis.
- **Es liegen keine Börsenschlüssel auf dem Server.** Der Papertrader kann
  technisch nichts kaufen.

Dateien:
`state/live.json`, `state/schatten.json` — Depotstände
`logs/taeglich.jsonl` — jeder Lauf vollständig
`logs/verlauf.csv` — Zeitreihe für die Auswertung
`logs/fehler.log` — nur bei Fehlern
`logs/telegram.log` — nur Sendeprobleme

---

## 6. Telegram

Es wird derselbe Bot benutzt wie für die beiden laufenden ETH-Bots.

### Einrichtung (einmalig, muss Ben selbst machen)

Die Zugangsdaten sind bewusst **nicht** automatisch vom eth-bot-Server
geholt worden: Ein Skript, das fremde Server nach Tokens durchsucht, ist
genau das Muster, das ein Sicherheitsfilter blockieren soll — und richtig
so. Der Token soll ausserdem weder durch einen Chatverlauf noch über einen
Arbeitsrechner laufen.

```bash
ssh root@204.168.228.46
cat > /root/ethtrading/06_Papertrading/telegram.env <<'EOF'
TELEGRAM_TOKEN=<Token des bestehenden Bots>
TELEGRAM_CHAT_ID=<dieselbe Chat-ID wie bei den ETH-Bots>
EOF
chmod 600 /root/ethtrading/06_Papertrading/telegram.env
cd /root/ethtrading/06_Papertrading
/root/ethtrading/.venv/bin/python papertrader.py --telegram-test
```

Ohne diese Datei läuft der Papertrader ganz normal weiter und notiert nur
in `logs/telegram.log`, dass er nichts senden konnte. **Telegram darf den
Handel nie stören** — jeder Sendefehler wird protokolliert und geschluckt.

### Was wann kommt

| Anlass | Zeitpunkt | Ton |
|---|---|---|
| Täglicher Stand | 00:02 UTC | **stumm**, wenn keine Order |
| Tag mit Order | 00:02 UTC | mit Ton |
| Wochenbericht | montags 00:02 UTC | mit Ton |
| Fehler im Lauf | sofort | mit Ton |

Der tägliche Stand kommt **auch an ruhigen Tagen** — er ist zugleich das
Lebenszeichen. An 79 % der Tage fällt keine Order; ohne tägliche Meldung
wäre ein abgestürzter Bot nicht von einem ruhigen Markt zu unterscheiden.
Damit das nachts nicht stört, sind diese Meldungen stumm gestellt.

Beispiel eines ruhigen Tages:

```
BTC-Papertrader  2026-08-25
Depot 10,000 EUR  (▬ +0.00 % seit Start)

BTC      77,734   SMA 4/4   Signal 1.00   Skala 1.00
ETH       2,463   SMA 4/4   Signal 1.00   Skala 0.99

Keine Order — Abweichung unter dem Totband.

Investiert 100 %   Orders gesamt 1   Lauf 2
Schatten (50/50): 10,000 EUR   +0.00 %
```

Der Wochenbericht am Montag ist der eigentliche Zwischenstand — er hakt die
vier Kriterien aus Abschnitt 3 ab:

```
WOCHENBERICHT BTC-Papertrader
2026-08-25 – 2026-09-01  (8 Tage)

✅ Läufe: 8/8 Tage
✅ Warnungen: 0
✅ Slippage BTC: 0.0180 % (Annahme 0,02 %)
❌ Orders: 0 = 0.0/Monat (erwartet 3–4)

Depot live     10,240 EUR   +2.40 %
Depot schatten 10,180 EUR   +1.80 %

Tag 8 von 60 (Minimum) bzw. 90 (empfohlen) bis zur Echtgeld-Entscheidung.
Die Rendite ist hier NICHT das Kriterium.
```

Ein ❌ in den ersten Wochen ist normal — bei 3–4 Orders im Monat kann die
Order-Zeile mehrere Wochen lang rot sein, ohne dass etwas kaputt ist.
Rot bleiben dürfen sie bis zur Echtgeld-Entscheidung nicht.
