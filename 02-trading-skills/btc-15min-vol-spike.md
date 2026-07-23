---
skill: btc-15min-vol-spike
families: [KXBTC15M]
signal_types: [vol-spike-candidate]
market_conditions: [live, midpoint, high-volatility]
confidence_threshold: 0.70
risk_profile: high
win_rate: null
sample_size: 0
status: draft  # STUB — thesis only; entry conditions TBD after sprint-4 data
last_updated: 2026-07-22
---

# BTC 15-Minute Vol Spike (stub)

> **Not wired yet:** `signal_types: [vol-spike-candidate]` above is a
> forward-looking type name — it is not yet in `kalshi_bots.types.SignalType`,
> and window-monitor's candidate detector only emits `fair-value-candidate`
> today. This note is therefore structurally inert (the matcher will never see
> a signal it declares for) until a vol-spike detector is built and the type
> is added. Intentional, not a typo — the same pattern `_skill-template.md`
> uses for pre-code specs.

## What this is
Thesis: when realized vol jumps sharply mid-window (short-window sigma running
far above the 900s baseline), Kalshi prices lag the new distribution — the
market keeps quoting yesterday's vol. A binary near the strike should reprice
toward 50/50 as vol expands; a laggy book leaves the wrong side rich for a few
minutes. This is the highest-risk idea in the library: vol spikes come from
news, and whoever fills you may know the news.

## Entry condition
TBD. Calibration needs sprint-4's vol-was-right postmortem series: how often
short-window vol spikes persist vs mean-revert within a window, and how fast
the book actually reprices. A spike-ratio threshold (e.g. sigma_60s /
sigma_900s) will only be proposed against that data.

## Invalidation / exit condition
TBD alongside entry. The universal near-close cutoff applies regardless.
Expect a hard time-stop: vol-lag trades either work fast or were wrong.

## Position sizing
Placeholder in `risk_management.py` (multiplier 0.5 — quarter-Kelly, per-trade
cap 3%, min depth 100): tighter than the other skills because adverse
selection around news is not captured by the Kelly edge estimate. All PROPOSED
and unused while this is a stub.

## Edge cases / do-not-trade list
TBD. Known from day one: never enter when the composite itself is degraded —
a "vol spike" measured off 2 surviving constituents is a feed artifact.

## Data dependencies
`crypto-price-feed` (multi-window sigma), `kalshi-ws-orderbook`,
`window-monitor`, `fair-value-model` (repricing reference), `risk-management`.
