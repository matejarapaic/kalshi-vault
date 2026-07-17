---
skill: garbage-time-mispricing
sports: [nfl, nba, mlb]
market_conditions: [live, blowout, endgame]
confidence_threshold: 0.60
risk_profile: low
win_rate: null
sample_size: 0
status: draft  # Category B — thresholds NOT confirmed; do not trade until status: confirmed
last_updated: 2026-07-17
---

# Garbage-Time Mispricing

## What this is
When a game is effectively decided, Kalshi prices often sit lazily below fair value
(favorite at 93¢ when the true probability is 99%+) because resting attention has
moved on and nobody is sweeping the last few cents. Buying the near-certain side
collects small, boring, high-win-rate edge. The entire skill lives or dies on fee
math and comeback tail risk, so both are explicit here.

## Entry condition
ALL of the following:

1. **Decided game**, per league-specific rules (ESPN win prob AND game state must
   both agree):
   - NFL: win prob ≥ 98%, AND lead > 16 points with < 6:00 remaining, opponent out
     of timeouts-adjusted possessions (lead > 2 scores + possession).
   - NBA: win prob ≥ 98%, AND lead ≥ 15 with < 4:00 remaining, or lead ≥ 9 with
     < 1:00 remaining.
   - MLB: win prob ≥ 98%, AND lead ≥ 5 runs entering the 9th, or lead ≥ 3 with
     2 outs in the 9th.
2. **Mispricing:** best YES ask for the winning side ≤ 95¢ (computed as
   1 − best NO bid where the YES book is empty — per kalshi-client gotcha).
3. **Fee-aware minimum edge:** expected net profit after Kalshi trading fees
   (fee ≈ ceil(0.07 × price × (1−price)) per contract) ≥ 1.5¢ per contract at the
   modeled ≥ 98% win probability. At 95¢ entry the fee is ~0.33¢ and modeled edge
   ≈ 3¢+ — passes. At 97.5¢ with modeled 98% it does not. The fee check is the
   entry condition; never trade this skill on gross edge.
4. **Book depth:** ≥ 300 contracts at or below the entry cap (this skill wants size
   to matter, since per-contract profit is pennies).

## Invalidation / exit condition
- **Default: hold to settlement.** The exit is the game ending.
- **Comeback stop:** ESPN win prob drops below 93%, OR the league lead condition in
  rule 1 no longer holds (e.g., NFL onside-kick recovery + score, MLB bases loaded
  in the 9th within 3 runs). Exit at market immediately — do not negotiate with a
  live comeback for the sake of 4¢.
- **Feed loss:** ESPN feed stale > 60s while position open in a non-final game →
  exit. Never hold blind for pennies.

## Position sizing
Flat sizing, not Kelly (Kelly explodes at p→1 and would suggest absurd size).
Per-trade cap: **5% of bankroll**. Additional correlation rule: total simultaneous
garbage-time exposure across ALL games capped at **10% of bankroll** — five
"sure things" failing on the same night is exactly the tail this rule exists for.
DRAFT NUMBERS — Category B, pending explicit confirmation.

## Edge cases / do-not-trade list
- NBA: never enter while the trailing team is intentionally fouling AND win prob
  < 99% — the fouling game extends tail scenarios beyond what the model prices.
- NFL: kneel-down formations = best case, enter freely; but a 16-point lead with
  4:00 left and the trailing team holding all timeouts fails rule 1 — respect it.
- MLB: extra-innings ghost-runner situations are NOT garbage time regardless of
  win prob. 9th inning rules only.
- Settlement risk: if the Kalshi market's settlement source differs from ESPN's
  final (rare, but rain-shortened MLB games are the known case) — the skill must
  check the market's settlement terms tag before entry; unknown terms = no trade.

## Data dependencies
`espn-data` (win prob + game state), `kalshi-client` (asks, depth, fee calc,
settlement terms), `league-matching`, `risk-management` (exposure aggregation).
