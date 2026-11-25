# ETHTRADING – öffentlicher Betriebsstand

Automatisch befüllt vom Bot-Server. Bitte nicht von Hand bearbeiten.
Das eigentliche Projekt liegt in einem privaten Repository; hier stehen
nur Zustände und Kennzahlen.

## Die Bots

| Bot | Betrieb |
|---|---|
| P1 Regime-Filter (BTC) | Papiergeld: zwei simulierte Depots, kein echtes Geld |
| P3 Rücklauf-Korb | echtes Geld, läuft aus: keine neuen Käufe mehr, offene Positionen schließen über die normale Ausstiegsregel, danach wird der Bot abgeschaltet |
| P4 Neulistungs-Short | echtes Geld, kleiner Einsatz |

## Was hier liegt

| Pfad | Inhalt |
|---|---|
| `daten/status.json` | Tageszustand aller Bots (Timer, letzter Lauf, Warnungen, Slippage, Ergebnis in Prozent), vom Wachhund täglich um 00:30 UTC geschrieben |
| `daten/taeglich.jsonl` | P1: jeder Lauf vollständig, eine Zeile je Tag |
| `daten/verlauf.csv` | P1: Zeitreihe der simulierten Depots, Signale, Slippage |
| `daten/depot_live.json` | P1: simuliertes Depot BTC 100 % |
| `daten/depot_schatten.json` | P1: simuliertes Vergleichsdepot BTC/ETH 50/50 |
| `referenz/` | P1: Betriebshandbuch und Erwartungen aus dem Rücktest |
| `review/` | Prüfberichte; der Server leitet neue `review/JJJJ-MM-TT.md` einmal an Telegram weiter |
| `routinen/` | Daten der Alltagsroutinen, `routinen/quittungen/` deren Zustellvermerke |

## Was hier nicht liegt

Keine Zugangsdaten, Schlüssel oder Tokens und keine Kontostände.
