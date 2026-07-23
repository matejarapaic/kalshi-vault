# Agent Roster

| Agent | Prompt | Role | Vault writes | Vault reads | Cadence | Order access |
|---|---|---|---|---|---|---|
| window-monitor | `01-agents/window-monitor/system-prompt.md` | Resolves + verifies the active 15-min window, tracks phases, logs samples, flags fair-value candidates | `03-market-context/active-windows/` | — (window resolution is clock + API, not vault) | Every orchestrator tick (1s); notes at 30s cadence | None (read-only market data) |
| trader | `01-agents/trader/system-prompt.md` | Re-verifies candidates from fresh data, sizes via risk-management, executes, manages exits | `04-trade-history/trades/` | `02-trading-skills/` (via skill-matcher) | Signal-driven entries; exit sweep every tick | **Sole order-placing agent**; demo autonomous / live manual-approve only |
| analyst | `01-agents/analyst/system-prompt.md` | Settlement polling, paper settlement, postmortems, batched rollups + skill stats | `04-trade-history/postmortems/` (daily aggregates), `03-market-context/active-windows/*` settlement fields, `02-trading-skills/*` frontmatter (`win_rate`/`sample_size` ONLY) | `04-trade-history/trades/`, `03-market-context/active-windows/` | Event-driven (window-close, ~96/day) + 5s settlement polls | None |

## Boundaries that matter
- Exactly one agent (trader) can place orders. If that ever stops being true, the
  system is misconfigured — halt.
- Exactly one agent (analyst) can write skill-note stats, and only the two stat
  fields (env-labeled `demo_*` variants included). Thresholds and conditions are
  human-edited only.
- All vault access on live cycles goes through the vault skill's cache. Direct file
  reads are a hard-rule violation (postmortem/backtest contexts exempt).
- An unresolved window (API verification failed) is watched, never traded — by
  every agent, with no override.
