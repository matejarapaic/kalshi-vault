---
skill: btc-15min-orderflow-imbalance
families: [KXBTC15M]
signal_types: [orderflow-candidate]
market_conditions: [live, midpoint]
confidence_threshold: 0.65
risk_profile: medium
win_rate: null
sample_size: 0
status: draft  # STUB — thesis only; entry conditions TBD after sprint-4 data
last_updated: 2026-07-22
---

# BTC 15-Minute Order-Flow Imbalance (stub)

## What this is
Thesis: persistent one-sided flow into a 15-minute book (bid depth growing
while ask depth drains, or trades printing repeatedly on one side) predicts
short-horizon drift in the Kalshi price that the fair-value model — which sees
only spot and vol — cannot. The edge is microstructure: front-running the
book's own imbalance before it moves the price, or fading an imbalance that
spot does not confirm.

## Entry condition
TBD. Calibration needs the sprint-4 postmortem corpus: per-window order-book
snapshot logs (30s cadence) and trade prints, so imbalance measures
(depth-ratio, signed trade flow) can be tested against realized window
outcomes before any threshold is proposed. No entry conditions will be
invented ahead of that data.

## Invalidation / exit condition
TBD alongside entry. The universal near-close cutoff applies regardless.

## Position sizing
Placeholder in `risk_management.py` (multiplier 1.0, per-trade cap 5%,
min depth 100) so the parameter table stays the single numeric home; all
PROPOSED and unused while this is a stub.

## Edge cases / do-not-trade list
TBD. Known from day one: ghost-spread risk in thin overnight books — an
"imbalance" of 30 contracts is noise, not flow.

## Data dependencies
`kalshi-ws-orderbook` (depth ladder over time, trade prints), `window-monitor`,
`crypto-price-feed` (spot confirmation), `risk-management`.
