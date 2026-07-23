---
skill: btc-15min-fair-value
families: [KXBTC15M]
signal_types: [fair-value-candidate]
market_conditions: [live, midpoint]
confidence_threshold: 0.60
risk_profile: medium
win_rate: null
sample_size: 0
status: confirmed  # owner override 2026-07-22: demo had 0 fillable liquidity to
                    # accumulate the intended >=30 samples; owner authorized live
                    # trading without that statistical validation, fully aware
last_updated: 2026-07-22
---

# BTC 15-Minute Fair Value

## What this is
At the 15-minute horizon there is no sharper external source — no consensus
line, no model feed about the *same* question. The reference truth is our own
volatility-based fair value: a drift-zero log-normal probability computed from
composite spot, the window strike, time remaining, and short-window realized
vol (`fair-value-model`, inputs from `crypto-price-feed`). The thesis: Kalshi's
implied probability diverges from that fair value by more than `MIN_EDGE_CENTS`
for microstructure reasons — a thin book, one-sided retail flow after a spot
move — and reverts toward fair value within the window. We buy the cheap side
and exit on convergence. The edge lives in vol measurement and book
microstructure, nowhere else; if `sigma` is wrong, this skill is wrong, which
is why the postmortem vol-was-right check (sprint-4) exists.

## Entry condition
ALL of the following, recomputed by the trader from fresh data at decision
time (never from the signal payload). Numeric homes: `risk_management.py`
(PROPOSED 2026-07-22).

1. **Edge:** signed edge for the entry side (model prob × 100 − that side's
   ask, from `fair-value-model.side_edges`) ≥ `MIN_EDGE_CENTS` (4¢).
2. **Phase:** window phase ∈ `ENTRY_PHASES` (`midpoint` only — i.e. from 2
   minutes after open until 3 minutes before close). No entries in `opening`
   (strike/book still settling) or `near_close` (gamma dominates; the model's
   assumptions break down).
3. **Depth:** ≥ `MIN_DEPTH_WITHIN_5C` (100) contracts within 5¢ of best, on
   EACH side of the book (`kalshi-ws-orderbook` snapshot).
4. **Feed health:** composite spot available with `constituents_healthy ≥ 2`
   (`crypto-price-feed`, fail-closed already below 2).
5. **Sigma plausible:** annualized realized vol within
   [`SIGMA_PLAUSIBLE_MIN`, `SIGMA_PLAUSIBLE_MAX`] = [20%, 200%]. Outside that
   band the number is either a broken feed or a market condition the model
   doesn't handle — no trade either way.
6. **Ask present** for the entry side (books are one-sided; a missing derived
   ask means there is nothing to buy).

## Invalidation / exit condition
Exits are mechanical, never approval-gated, evaluated every tick:

- **Near-close cutoff (universal):** window enters `near_close` → exit at
  market. Take whatever P&L exists rather than hold to settlement noise.
- **Thesis played out (take profit):** held side's edge ≤ `EXIT_EDGE_CENTS`
  (1¢) → exit.
- **Thesis broken (stop):** the *other* side's edge turns positive (model is
  now on the other side of the market) → exit immediately; do not wait for
  further deterioration.
- **Book invalidation:** depth within 5¢ on either side drops below
  `MIN_DEPTH_WITHIN_5C × DEPTH_COLLAPSE_FRACTION` (50 contracts) → exit.
- **Feed invalidation:** composite unavailable (constituent dropout) or sigma
  unavailable → exit (`feed_loss`). Never hold a model-driven position blind.

## Position sizing
Half-Kelly on the measured edge (`BASE_KELLY_FRACTION` 0.5 × skill multiplier
1.0), per-trade cap 5% of bankroll, all through `risk-management`'s cap
pipeline. While `status: draft`, additionally hard-capped at
`MAX_CONTRACTS_PER_WINDOW` (20 contracts).

## Edge cases / do-not-trade list
- **Thin overnight/weekend books:** the depth gate (entry 3) is the mechanical
  self-throttle; do not weaken it to "get more samples."
- **Vol regime break in-window** (news shock): sigma lags reality for up to
  its 900s window; the sigma band (entry 5) bounds but does not eliminate
  this. Expect postmortems to attribute losses here first.
- **Sub-cent tails:** at model probs > ~0.90 the book trades in $0.001 ticks
  that our cent aggregation blurs; combined with the near-close cutoff this
  skill should rarely be there — treat repeated tail entries as a bug signal.
- **Demo book vs prod book:** demo quotes are thin/stale; paper fills come
  from prod public books (PaperBroker). Stats accumulated against demo books
  directly would be meaningless.

## Data dependencies
`crypto-price-feed` (spot composite, realized vol), `kalshi-ws-orderbook`
(book snapshot, BRTI stream), `window-monitor` (window/phase/strike),
`fair-value-model` (probability + edges), `risk-management` (all numbers),
`kalshi-client` (execution).
