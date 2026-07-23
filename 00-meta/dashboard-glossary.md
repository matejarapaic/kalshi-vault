# Reading the "Active Window" Dashboard Card

Plain-English reference for the numbers on the dashboard's per-window card,
for the owner, not the agents. Nothing here is read by code — it's a human
cheat sheet. Ties back to `fair-value-model` and `risk_management.py`, the
actual code these numbers come from.

## The fields, top to bottom

| Field | What it is | Where it comes from |
|---|---|---|
| `KXBTC15M-...` ticker + phase badge | Which 15-min window, and its lifecycle stage: `opening` → `midpoint` → `near_close` → `settled` | window-monitor |
| Countdown ("3:33 to close") | Wall-clock time left in the window | window-monitor |
| Spot (composite) | The multi-exchange weighted-median BTC/USD price right now — the system's approximation of CF Benchmarks' BRTI | `crypto-price-feed` |
| Strike | The price level this window's contract resolves against. Settles YES if the 60s-average settlement value is **>= strike** (tie goes to yes) | resolved once at window open |
| Model P(up) | The model's estimate of the probability BTC finishes at/above strike, shown as a fair-value price in cents (e.g. `8.2¢` means the model thinks YES is worth 8.2 out of 100) | `fair_value_model.fair_value_prob` — drift-zero log-normal from spot, strike, time remaining, sigma |
| σ (annualized) | Realized volatility the model is currently plugging in, annualized. This is the single biggest lever on Model P(up) — a wrong sigma is the model's main failure mode | rolling realized-vol estimator, `crypto-price-feed` |
| Market yes bid/ask | What Kalshi's actual order book shows right now for the YES side. The **ask** is what the model gets compared against (no-side ask is derived as `100 - yes_bid` since Kalshi books are bids-only) | `kalshi-ws-orderbook` |
| Edge (model − ask) | `Model P(up)*100 - yes_ask`, in **cents**, signed. Positive (green) = model thinks YES is underpriced by the market; negative (red) = market is pricing YES *above* what the model thinks it's worth. The `no` side has its own, separate edge computed the same way against the no-ask — the two are not mirror images once there's a spread | `fair_value_model.side_edges` |

## What actually triggers a trade

Edge alone isn't enough — ALL of these must hold at once (`btc-15min-fair-value`
skill, entry conditions, re-checked from fresh data at decision time):

1. **Edge ≥ 4¢** (`MIN_EDGE_CENTS`) on whichever side (yes or no) has the larger edge.
2. **Phase = midpoint only.** No entries in `opening` (strike/book still settling) or `near_close` (gamma dominates, model assumptions break down).
3. **Depth ≥ 100 contracts** within 5¢ of best, on both sides of the book.
4. **≥2 healthy feed constituents** — thin/broken composite feed = no trade.
5. **σ inside [20%, 200%] annualized** — outside that band the number is either a broken feed or a regime the model doesn't handle, so no trade either way.
6. **An ask actually exists** on the entry side (books are one-sided; no ask = nothing to buy).

So a screenshot like `4.2¢` edge at `midpoint` phase with a live 4/4 book is
the shape of an actual entry candidate; `-0.2¢` or an edge in `opening`/`near_close`
is "watched, not traded" by design, not a bug.

## Once in a trade, what closes it

Exits are mechanical, never approval-gated, checked every tick:
- **near_close** → exit at market regardless of P&L (take what exists rather than hold into settlement noise).
- **Edge ≤ 1¢** (`EXIT_EDGE_CENTS`) on the held side → take-profit exit, thesis played out.
- **The other side's edge turns positive** → stop-out, the model has flipped sides.
- **Depth collapses** below 50 (half of `MIN_DEPTH_WITHIN_5C`) on either side → exit, the book can't be trusted.

## What to actually watch for, practically

- **Edge and sigma moving together**: if σ jumps around a lot between refreshes, the vol estimator may be reacting to a feed glitch rather than real volatility — that's exactly what the sigma-plausible-band gate exists to catch.
- **Edge sitting just under 4¢ repeatedly**: means the skill is seeing candidates but nothing crosses threshold — check the postmortem daily note for how many were logged as "declined" counterfactuals and what they would have done.
- **A `settled_direction` that keeps disagreeing with `Model P(up)`**: the postmortem's model-was-right check flags this per window; if it recurs, sigma or the model itself is off, not just noise.
- **`win_rate`/`sample_size` on the skill note**: currently 0 (this skill just went live with no prior validated track record — see pivot status in the repo's `CLAUDE.md`). Below 20 samples the number is meaningless; a `⚠ THRESHOLD REVIEW` flag only becomes possible past that floor.
