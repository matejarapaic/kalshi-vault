---
skill: live-win-prob-overreaction
sports: [nfl, nba, mlb]
market_conditions: [live, high-volatility, momentum-swing]
confidence_threshold: 0.65
risk_profile: medium
win_rate: null
sample_size: 0
status: confirmed  # thresholds explicitly approved by owner 2026-07-17
last_updated: 2026-07-17
---

# Live Win-Probability Overreaction

## What this is
Live prediction markets systematically overshoot on emotionally salient plays — a
pick-six, a 12-0 scoring run, a bases-clearing double. Retail flow chases the
momentum and pushes the Kalshi price past what the underlying win-probability model
says the play was actually worth. The edge is fading that overshoot: trading *against*
the recent move, toward the model.

## Entry condition
ALL of the following, evaluated on a single polling cycle:

1. **Swing trigger:** ESPN win probability for one team moved ≥ 15 points within the
   last 4 minutes of real time (from `espn-data` win-prob series).
2. **Overshoot:** Kalshi de-vigged mid-price (from `kalshi-client`, orderbook-based,
   never summary fields) for that team exceeds current ESPN win probability by
   ≥ 8 points in the direction of the swing. (Price 78¢ vs. model 68% = 10-pt
   overshoot → fade by buying the other side.)
3. **Persistence:** the ≥ 8-pt gap has held for 2 consecutive polling cycles (~40s).
   One-cycle gaps are usually the model lagging the feed, not the market overreacting.
4. **Not endgame:** more than 5:00 remaining (NFL/NBA game clock) or before the top
   of the 8th (MLB). Late-game prices legitimately decouple from smooth model output.
5. **Book depth:** ≥ 200 contracts available within 2¢ of the target entry price
   (from `kalshi-client` depth check). Thin books = no trade, regardless of edge.
6. **No news explanation:** no injury flag on either team within the last 10 minutes
   (from `espn-data` injury feed). If news explains the move, the market may be
   *right* and the model lagging — that's the injury skill's territory, not this one.

## Invalidation / exit condition
Exit (market order acceptable) when ANY of:

- **Convergence (take profit):** gap between Kalshi mid and ESPN win prob closes to
  ≤ 3 points. That was the whole thesis.
- **Thesis wrong (stop):** gap *widens* to ≥ 15 points — the market knows something
  the model doesn't; get out, don't average down.
- **News invalidation:** injury/ejection news arrives on either team.
- **Endgame cutoff:** game reaches 5:00 remaining / top 8th — exit whatever remains,
  win or lose. This skill has no endgame edge.
- **Time stop:** position open 20 minutes of real time with gap between 3 and 8 pts
  (drifting, not converging) — exit at market.

## Position sizing
Half-Kelly on the measured edge (Kalshi de-vigged price vs. ESPN model prob), through
the central `risk-management` skill. Per-trade cap: **5% of bankroll**. This skill
gets a 1.0× risk multiplier (baseline).
Numbers confirmed by owner 2026-07-17.

## Edge cases / do-not-trade list
- ESPN win-prob feed stale (last update > 90s old) → the "gap" is garbage. No trade.
- First 5 minutes of any game — model priors dominate, gaps are model noise.
- Doubleheaders / suspended-resumed MLB games — game-state mapping unreliable.
- Kalshi spread > 4¢ — you'll donate the edge to the spread crossing in and out.
- Overtime/extra innings: treat as endgame (rule 4 already excludes, but explicit).

## Data dependencies
`espn-data` (win prob series, injury feed), `kalshi-client` (de-vigged mid, book
depth), `league-matching` (game↔market resolution), `risk-management` (sizing).
