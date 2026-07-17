# Agent Roster

| Agent | Prompt | Role | Vault writes | Vault reads | Cadence | Order access |
|---|---|---|---|---|---|---|
| game-monitor | `01-agents/game-monitor/system-prompt.md` | Live game & market state watcher; emits candidate signals | `03-market-context/active-games/` | `00-meta/league-config.md`, `03-market-context/daily-slate/` | Polling ramp (see league-config) | None (read-only market data) |
| trader | `01-agents/trader/system-prompt.md` | Verifies signals, sizes via risk-management, executes (demo) | `04-trade-history/trades/` | `03-market-context/active-games/`, `02-trading-skills/` (via skill-matcher), `00-meta/league-config.md` | Signal-driven + position-management poll | **Sole order-placing agent**; demo env; approval-gated pending execution-mode decision |
| analyst | `01-agents/analyst/system-prompt.md` | Postmortems, skill stats, daily slate | `04-trade-history/postmortems/`, `02-trading-skills/*` frontmatter (`win_rate`/`sample_size` ONLY), `03-market-context/daily-slate/` | `04-trade-history/trades/`, `03-market-context/active-games/` | Event-driven (game-final) + nightly | None |

## Boundaries that matter
- Exactly one agent (trader) can place orders. If that ever stops being true, the
  system is misconfigured — halt.
- Exactly one agent (analyst) can write skill-note stats, and only the two stat
  fields. Thresholds and conditions are human-edited only.
- All vault access on live cycles goes through the vault skill's cache. Direct file
  reads are a hard-rule violation (postmortem/backtest contexts exempt).
