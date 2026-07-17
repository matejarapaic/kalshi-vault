---
skill: injury-news-repricing-lag
sports: [nfl, nba, mlb]
market_conditions: [live, pregame, news-event]
confidence_threshold: 0.75
risk_profile: high
win_rate: null
sample_size: 0
status: draft  # Category B — thresholds NOT confirmed; do not trade until status: confirmed
last_updated: 2026-07-17
---

# Injury-News Repricing Lag

## What this is
When a materially important player is ruled out (or exits a live game), sportsbooks
reprice within seconds-to-minutes; Kalshi's sports books often lag by several
minutes. The edge is buying/selling Kalshi at pre-news prices after the outcome
distribution has already shifted. This is the highest-risk skill in the library:
it is news-driven, adverse-selection-prone (whoever fills you may know more than
you), and the confirmation window is short.

## Entry condition
ALL of the following:

1. **Material player:** the flagged player is a starter AND on the skill's
   league-specific materiality list: NFL — starting QB only (other positions do not
   move win prob enough to clear fees reliably); NBA — a top-2 minutes-per-game
   player on their team; MLB — starting pitcher (announced scratch or in-game exit
   before the 5th inning).
2. **Status certainty:** ESPN injury feed status is OUT or player has physically
   left a live game (`espn-data`). QUESTIONABLE/DOUBTFUL/day-to-day = no trade, ever.
3. **Books repriced, Kalshi hasn't:** sportsbook consensus (via `odds-api`) has moved
   ≥ 4 points since the news timestamp, AND Kalshi de-vigged mid has moved < 2 points
   from its pre-news level. This is the lag we're paid for — both halves must be true.
4. **Lag window:** ≤ 10 minutes since news timestamp. Past 10 minutes, assume the
   lag has been arbitraged and anything left is adverse selection.
5. **Book depth:** ≥ 100 contracts within 2¢ (lower bar than other skills — this
   trade tolerates less size by design, see sizing).

## Invalidation / exit condition
- **Repricing complete (take profit):** Kalshi mid within 2 points of post-news
  sportsbook consensus.
- **News reversed:** player returns / status upgraded → exit immediately at market,
  accept the loss.
- **Second-source failure:** if within 5 minutes of entry the move is not
  corroborated (sportsbook consensus retraces > half its post-news move), treat the
  original signal as noise and exit.
- **Hard time stop:** 30 minutes after entry regardless of P&L. Lag trades either
  work fast or they were wrong.

## Position sizing
Quarter-Kelly (0.5× the standard half-Kelly multiplier) via `risk-management`.
Per-trade cap: **3% of bankroll** — tighter than other skills because sizing into
news flow carries adverse-selection risk that the Kelly edge estimate doesn't capture.
DRAFT NUMBERS — Category B, pending explicit confirmation.

## Edge cases / do-not-trade list
- Never trade on social-media-only reports; entry requires the ESPN feed flag.
  (Slower but verifiable — the skill accepts missing the fastest fills.)
- NFL inactives dumps (90 min pregame): dozens of flags at once; only the QB rule
  applies, ignore the rest.
- MLB bullpen games / announced "openers": starter-exit logic doesn't apply.
- If BOTH teams have qualifying news in the window, stand down — net effect unclear.
- Backup player quality matters and is NOT modeled — this is priced into the tighter
  cap, but postmortems must track whether backup-quality misses cluster.

## Data dependencies
`espn-data` (injury feed — primary trigger), `odds-api` (corroboration + target),
`kalshi-client`, `league-matching`, `risk-management`.
