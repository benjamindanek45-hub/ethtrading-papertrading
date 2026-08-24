# ETH/BTC Papertrading — Datenbruecke

Automatisch befuellt vom GoldBotServer. **Nicht von Hand bearbeiten**,
ausser im Ordner `review/`.

## Was hier liegt

| Pfad | Inhalt |
|---|---|
| `daten/taeglich.jsonl` | jeder Handelslauf vollstaendig, eine Zeile je Tag |
| `daten/verlauf.csv` | Zeitreihe: Depotwert, Signale, Slippage |
| `daten/depot_live.json` | Depotstand BTC 100 % |
| `daten/depot_schatten.json` | Depotstand BTC/ETH 50/50 |
| `referenz/` | Betriebshandbuch und Backtest-Erwartungen |
| `review/` | **hier legt der Agent seine Berichte ab** |

## Fuer den Agenten

Ein Bericht als `review/JJJJ-MM-TT.md` wird vom Server beim naechsten
taeglichen Lauf erkannt und automatisch an Telegram weitergeleitet — genau
einmal. Der Agent schickt selbst nichts an Telegram; das Token liegt nur
auf dem Server.

## Was hier NICHT liegt

Keine Zugangsdaten, keine Schluessel, kein echtes Geld. Dies ist reines
Papertrading: simulierte Depots mit je 10.000 EUR fiktivem Kapital.
