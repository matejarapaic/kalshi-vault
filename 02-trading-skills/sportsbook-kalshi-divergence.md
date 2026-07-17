---
skill: sportsbook-kalshi-divergence
sports: [nfl, nba, mlb]
market_conditions: [live, pregame]
confidence_threshold: 0.70
risk_profile: medium
win_rate: null
sample_size: 0
status: confirmed  # thresholds explicitly approved by owner 2026-07-17
last_updated: 2026-07-17
---

# Sportsbook ↔ Kalshi Divergence

## What this is
Sportsbooks run sharper models and see sharper flow than Kalshi's sports books do.
When the de-vigged consensus of multiple major sportsbooks disagrees with Kalshi's
de-vigged price and the disagreement is stable, Kalshi is usually the one that's
wrong. Trade Kalshi toward the sportsbook consensus and wait for convergence.
This is the bread-and-butter skill — highest expected trade count.

## Entry condition
ALL of the following:

1. **Consensus basis:** de-vigged implied probability from ≥ 3 major sportsbooks
   (via `odds-api`), max pairwise disagreement among those books ≤ 3 points. If the
   books disagree with *each other*, there is no consensus to trade toward.
2. **Divergence:** |sportsbook consensus prob − Kalshi de-vigged mid| ≥ 5 points.
3. **Stability:** divergence ≥ 5 points on 2 consecutive `odds-api` polling cycles.
   Guards against trading a stale quote on either side during fast game action.
4. **Freshness:** both the odds-api snapshot and Kalshi orderbook read are < 60s old
   at decision time (< 120s for pregame markets).
5. **Direction check:** the divergence must be *shrinking or flat* on the books' side —
   i.e., the sportsbooks are not themselves racing toward Kalshi's number. If books
   moved ≥ 2 points toward Kalshi over the last 2 cycles, Kalshi led the move and the
   books are catching up. Do not fade the leader.
6. **Book depth:** ≥ 200 contracts within 2¢ of target entry; Kalshi spread ≤ 3¢.

## Invalidation / exit condition
- **Convergence (take profit):** divergence ≤ 2 points.
- **Thesis wrong (stop):** the sportsbook consensus moves to within 2 points of your
  *entry-side* Kalshi price — the books came to Kalshi, meaning Kalshi was right and
  you're now holding the wrong side. Exit immediately.
- **Consensus breaks:** pairwise book disagreement exceeds 5 points (books in
  conflict — no signal). Exit at best available.
- **Data loss:** odds-api feed stale > 3 minutes during a live game → flatten. Never
  hold a divergence position blind.
- **Game-final:** hold-to-settlement is allowed ONLY if divergence at final whistle
  still favors the position and price ≥ 90¢; otherwise exit before endgame chaos
  (same 5:00 / 8th-inning cutoff as the overreaction skill).

## Position sizing
Half-Kelly on measured edge (consensus prob vs. Kalshi price, fees included), via
`risk-management`. Per-trade cap: **5% of bankroll**. Risk multiplier 1.0×.
Pregame entries: half size (0.5× multiplier) — pregame edges are smaller and slower
to converge, tying up exposure.
Numbers confirmed by owner 2026-07-17.

## Edge cases / do-not-trade list
- Odds-format conversion must come from `odds-api`'s tested converter only — never
  inline math (this is the documented silent-error hotspot).
- Books post different market shapes (3-way lines, ties) — only compare true
  two-way win markets to Kalshi two-way markets.
- MLB: books may quote "live" odds frozen during a pitching change/review while
  Kalshi keeps trading — freshness rule (4) exists for exactly this; respect it.
- Very short-priced markets (consensus > 92% or < 8%): fee drag dominates the edge.
  Defer to the garbage-time skill's fee-aware entry instead.

## Data dependencies
`odds-api` (consensus, de-vig), `kalshi-client` (de-vigged mid, depth, fees),
`league-matching`, `risk-management`.
