---
name: trading-cycle
description: Run one hourly trading cycle for the Robinhood momentum/news agent. It syncs the sleeve, manages exits, scans for new setups, posts proposals for approval and updates the journal. Use it when the scheduled Routine fires or when Luke asks to "run the cycle", "check the market" or "any trades?".
---

# Trading cycle

Read `CLAUDE.md` (hard rules) and `STRATEGY.md` first. The hard rules win over
anything in this skill.

## 0. Preflight
- Confirm the Robinhood connector tools are available. If they aren't, tell Luke
  to connect Robinhood at https://claude.ai/customize/connectors and start a new
  session, then stop. Don't guess tool names.
- Tools (connector `Robinhood`). Read tools: `get_accounts`, `get_portfolio`,
  `get_equity_positions`, `get_equity_orders`, `get_equity_quotes`,
  `get_equity_fundamentals`, `get_equity_technical_indicators`,
  `get_equity_historicals`, `get_equity_news`, `get_earnings_calendar`,
  `get_indexes` + `get_index_quotes`. Write tools: `review_equity_order`, then
  `place_equity_order` and `cancel_equity_order`. Use them **only** for approved
  proposals. Never call `create_watchlist`, `create_alert` or `create_scan`
  unless Luke asks for it.
- If sleeve cash is $0, still run the analysis, but label every proposal
  "unfunded: can't be placed".
- Get the current time in ET. If the market is closed (weekend, holiday, outside
  9:30–16:00 ET), post a one-line "market closed" note and stop. Don't commit
  anything.
- If `journal/positions.md` says `PAUSED` or `HALTED`, only sync and report. No proposals.

## 1. Sync the sleeve
- Pull positions, open orders and recent fills from Robinhood.
- Reconcile with `journal/positions.md`: record fills of approved orders, stops
  that triggered and cancellations. Flag any mismatch to Luke instead of guessing.
- Compute sleeve cost basis, market value, today's P&L and P&L since inception.
- Check the daily loss stop (−$50) and the drawdown halt (−$150). If the halt
  triggers, set `HALTED` in `journal/positions.md` and notify Luke.
- Mark proposals past their expiry as `EXPIRED` in `journal/proposals.md`.

## 2. Manage exits (open sleeve positions)
Apply the exit and trailing rules from `STRATEGY.md`. Anything that needs an order
becomes a SELL (or STOP-UPDATE) proposal. The only order that goes in without
fresh approval is the stop-loss already approved with its buy.

## 3. Scan for entries (skip if at a limit or paused/halted)
- Candidates: `journal/watchlist.md`, today's top movers on volume, and names in
  the news with fresh catalysts (web search).
- Filter each one through every Entry rule in `STRATEGY.md` and the universe rules in
  `CLAUDE.md`. Drop anything you can't verify.
- Keep at most the best 2 per run, and stay within the 4-per-day limit.

## 4. Report (keep it short; Luke reads it on his phone)
```
🕙 10:45 ET | Sleeve $1,012 (+1.2%) | Today +$8 | 3/5 positions | $540 cash
Positions: AAPL +3.1% (stop $221) · AMD −1.4% (stop $148) · …
Exits: none
Proposals:
  <proposal blocks in the CLAUDE.md format>
Reply "approve P-…" / "reject P-…" · "pause" to stop
```
If there's nothing to do, send one line: `🕙 11:45 ET | Sleeve $1,009 | no setups | 3/5 positions`.

## 5. On approval (when Luke replies)
1. Re-quote. If the proposal has expired or the price moved more than 1.5%, say so and re-propose. Don't place it.
2. Re-check every hard rule against the current state.
3. Place the limit order. When it fills, place the stop order.
4. Report the exact Robinhood response (order ID, status, fill price).
5. Update `journal/proposals.md`, `journal/positions.md` and `journal/trades.md`.

## 6. Persist
Commit `journal/` with message `journal: <date> <time> ET cycle` and push to the
current branch. Skip the commit if nothing changed.
