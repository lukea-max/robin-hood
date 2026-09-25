---
name: trading-cycle
description: Run one hourly trading cycle for the Robinhood momentum/news agent. It syncs the sleeve, manages exits, scans for new setups, places trades autonomously within the CLAUDE.md limits, reports them and updates the journal. Use it when the scheduled Routine fires or when Luke asks to "run the cycle", "check the market" or "any trades?".
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
  `place_equity_order` and `cancel_equity_order`. Use them only for trades that
  pass every hard rule (or approved proposals in `approve mode`). Never call `create_watchlist`, `create_alert` or `create_scan`
  unless Luke asks for it or it's the watchlist/alert upkeep he delegated
  (adding/removing watchlist names and their alerts, fractional soft-stop alerts).
- If sleeve cash is below the planned order size plus the buffer, still run the
  analysis but place nothing. Report "insufficient cash".
- Get the current time in ET. If the market is closed (weekend, holiday, outside
  9:30–16:00 ET), post a one-line "market closed" note and stop. Don't commit
  anything.
- If `journal/positions.md` says `PAUSED` or `HALTED`, only sync and report. Place no new orders (stop orders already in place stay).

## 1. Sync the sleeve
- Pull positions, open orders and recent fills from Robinhood.
- Reconcile with `journal/positions.md`: record fills, stops
  that triggered and cancellations. Flag any mismatch to Luke instead of guessing.
- Compute sleeve cost basis, market value, today's P&L and P&L since inception.
- Check the daily loss stop (−5% of S) and the drawdown halt (−15% of S). If the halt
  triggers, set `HALTED` in `journal/positions.md` and notify Luke.
- Cancel unfilled buy orders older than 30 minutes, or whose price moved more than 1.5% away. Mark them `EXPIRED`.

## 2. Manage exits (open sleeve positions)
Apply the exit and trailing rules from `STRATEGY.md`. Execute exits and stop
updates directly (limit sells at or near the bid; replace stops by cancelling
the old one, then placing the new one). Mind the same-day round-trip rule.

## 3. Scan for entries (skip if at a limit or paused/halted)
- Candidates: `journal/watchlist.md`, today's top movers on volume, and names in
  the news with fresh catalysts (web search).
- Filter each one through every Entry rule in `STRATEGY.md` and the universe rules in
  `CLAUDE.md`. Drop anything you can't verify.
- Take at most the best 1 per run, and stay within the daily buy limit and position limit.
- Execute: fresh quote (< 2 min old) → re-check every hard rule →
  `review_equity_order` (any alert → skip and report) → `place_equity_order`:
  - whole shares: limit, ≤ 0.5% above ask → once filled, place the GTC stop order;
  - fractional (1 share > size): `type=market`, `dollar_amount`, regular hours, only if
    the spread is ≤ 0.15% → once filled, create a `price_below` alert at the stop (soft
    stop) and record "SOFT" as the stop order ID in `journal/positions.md`.
  Report the exact Robinhood response.
- Soft stops: every run, before anything else in step 2, quote each SOFT-stop position;
  at or below its stop → market sell the full quantity (regular hours) and push.

## 3b. Options test (only while `journal/positions.md` says "Options test: OPEN")
- Follow `CLAUDE.md` § Options test. Tools: `get_option_chains`, `get_option_instruments`,
  `get_option_quotes`, `review_option_order`, `place_option_order`, `cancel_option_order`,
  `get_option_orders`, `get_option_positions`.
- APPROVE FIRST: pick the best qualifying call or put (at most 1), post it as a proposal with a
  push, log it in `journal/proposals.md` and place it only after Luke writes `approve P-<id>`.
  Once it's placed set "Options test: USED" in `journal/positions.md`.
- Every run after that: quote the contract and apply the +50% / −50% / 2-days-before-expiry /
  thesis exits. Record fills in `journal/trades.md`.

## 4. Report (keep it short; Luke reads it on his phone)
```
🕙 10:45 ET | Sleeve $203 (+1.5%) | Today +$3 | 1/2 positions | $118 cash
Positions: IONQ +3.1% (stop $43.10)
Orders placed this run:
  <trade records in the CLAUDE.md format, with order ID and status>
Skipped: <ticker: reason> (if relevant)
Reply "pause" to stop trading · "sell all" to exit everything
```
If there's nothing to do, send one line: `🕙 11:45 ET | Sleeve $201 | no trades | 1/2 positions`.

## 5. Approve mode only (after Luke writes `approve mode`)
Post proposals instead of placing orders. When Luke approves one:
1. Re-quote. If the proposal has expired or the price moved more than 1.5%, say so and re-propose. Don't place it.
2. Re-check every hard rule against the current state.
3. Place the limit order. When it fills, place the stop order.
4. Report the exact Robinhood response (order ID, status, fill price).
5. Update `journal/proposals.md`, `journal/positions.md` and `journal/trades.md`.

## 5b. Alerts
- Send a `PushNotification` for every fill, triggered stop, rejection, loss-stop or halt
  event (see `CLAUDE.md` § Alerts), and ALWAYS end the run with a one-line push summary
  (even "no trades").
- After any entry or exit, update the Robinhood price alerts and `journal/alerts.md`.

## 6. Persist
Commit `journal/` with message `journal: <date> <time> ET cycle` and push to the
current branch. Skip the commit if nothing changed.
