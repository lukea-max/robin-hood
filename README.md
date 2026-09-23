# Robin Hood: my trading agent

A Claude-powered momentum/news trading agent for a **$1,000 sleeve** of my
Robinhood account. It runs every hour during market hours, researches setups and
**proposes** trades. Nothing is placed until I reply `approve P-<id>`.

| File | What it is |
|---|---|
| `CLAUDE.md` | The agent's hard rules: approvals, limits, risk stops |
| `STRATEGY.md` | The momentum + news playbook (edit it to change the strategy) |
| `.claude/skills/trading-cycle/SKILL.md` | What happens on each hourly run |
| `journal/` | Positions, proposals, fills and watchlist, committed after every run |

## Setup
1. **Connect Robinhood**: at https://claude.ai/customize/connectors, open
   *Robinhood* and finish sign-in (server: `https://agent.robinhood.com/mcp/trading`).
2. **Start a new Claude Code session** on this repo so the connector loads.
3. Say: *"Do a dry run of the trading cycle, read-only."* Check that it can see your
   account, then fill in `journal/watchlist.md` if you like.
4. Say: *"Create the hourly Routine for this session."* It should use
   - cron `45 13-19 * * 1-5` (UTC) = 9:45–15:45 ET on weekdays while daylight
     saving time is on. **After Nov 1, change it to `45 14-20 * * 1-5`.**
   - prompt: `Run the trading-cycle skill.`
   - connector: Robinhood

   It fires into that same session, so I can reply `approve P-…` right there.

## Commands (in the session)
- `approve P-<id>` / `reject P-<id>` / `approve all`
- `pause` / `resume`: stop or restart new proposals
- `run the cycle`: run a cycle now
- `status`: sleeve summary

## Warning
Momentum trading is high-risk, and this is not financial advice. The agent can be
wrong, and data can be stale or missing. Keep the sleeve small, read every
proposal before approving, and change the limits only in `CLAUDE.md`.
