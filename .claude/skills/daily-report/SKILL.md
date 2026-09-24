---
name: daily-report
description: Write one of Luke's four daily trading reports (pre-market, midday, pre-close, after-close) for the Robinhood agent's sleeve. Read-only; never places orders. Use when a scheduled report Routine fires or Luke asks for a report, recap, "how's it going", or "status".
---

# Daily report

Reports are **read-only**. Never place, change or cancel orders from this skill.
The hourly `trading-cycle` does the trading. Keep each report short enough to
read on a phone. Use real numbers from Robinhood only, never estimates. If data
is missing, say so.

## Data to pull (every report)
- `get_portfolio` (Agentic account): total value, cash.
- `get_equity_positions` + `get_equity_orders` (today): holdings, fills, open stop orders.
- `get_equity_quotes` for SPY, every sleeve position and every `journal/watchlist.md` ticker.
- `get_alert_log`: any Robinhood alerts that fired since the last report.
- P&L vs sleeve capital S (`journal/positions.md`); distance to the daily loss stop (−5% S) and the halt (−15% S).

## Sections

### pre-market (≈ 9:05 ET)
```
☀️ PRE-MARKET · <date>
Sleeve $X (±$ / ±% since start) · cash $X · N/2 positions
Overnight: <each position: pre-market price vs yesterday's close, distance to stop>
Market: SPY pre-market ±% · above/below 50-day average $X · macro events today (CPI, Fed, etc. via news)
Catalysts: <earnings or news today for positions and watchlist names>
Game plan: <what the 9:45 run will look for; which watchlist names are in or out of range>
```

### midday (≈ 12:30 ET)
```
🕛 MIDDAY · <date>
Sleeve $X · today ±$ (±%) · cash $X
Positions: <ticker price, ±% today, ±$ since buying, stop, target>
Trades so far today: <fills with order IDs, or "none">
Watchlist: <one line each: price, ±% today, in or out of range>
Risk: $X used of today's −$10 loss limit
```

### pre-close (≈ 3:30 ET)
```
🕞 PRE-CLOSE · <date>
Sleeve $X · today ±$ · positions held overnight: <list, each with its stop>
Overnight risk: <earnings or events after the close or tomorrow for held names>
No new buys after 15:30 (rule). Stops are GTC and stay active.
```

### after-close (≈ 4:15 ET)
```
🌙 CLOSE · <date>
Sleeve $X (day ±$ / ±%, since start ±$ / ±%) · realized P&L today $X
Trades today: <buys and sells with fill prices>
Scorecard: <trades to date, win/loss, biggest winner and loser>
What worked / what didn't: <1–2 lines, honest>
Tomorrow: <watchlist setups, earnings, events>
```
Also add a dated block to `journal/reports.md` (after-close only needs the full text; the other
reports get one line), then commit and push.

## Alerts (every report)
- Say which Robinhood alerts fired (from `get_alert_log`).
- Keep the Robinhood alerts in sync (see `CLAUDE.md` § Alerts): one alert just above each
  position's stop, one at its +6% break-even trigger, and one at each watchlist name's
  breakout level. Delete alerts for names that are no longer held or watched.
