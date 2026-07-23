---
skill: <name>
families: [KXBTC15M]  # market families this skill trades; [all] for any
signal_types: [fair-value-candidate]  # CryptoSignal types this skill answers
market_conditions: []  # tags the skill-matcher filters on, e.g. [live, opening, midpoint, high-volatility, thin-book]
confidence_threshold: <number>  # 0–1; minimum matcher score required to act on this skill
risk_profile: <low|medium|high>
win_rate: null      # maintained by postmortem skill — never hand-edit
sample_size: 0      # maintained by postmortem skill — never hand-edit
status: draft       # draft | confirmed | retired — only 'confirmed' skills may trade
last_updated: <YYYY-MM-DD>
---

# <Skill Name>

## What this is
One paragraph, plain language: the market inefficiency this skill exploits and why
it should exist at all. At the 15-minute crypto horizon there is no external
"sharper source" — the edge must come from volatility modeling or microstructure,
and this section must say which.

## Entry condition
Precise and mechanical. Numbers, not vibes. Every quantity must name its data source
(which skill produces it: crypto-price-feed, kalshi-ws-orderbook, fair-value-model,
window-monitor). If a human couldn't hand-check the condition against a screenshot
of the data, it's not specific enough. Include the window phases in which entry is
allowed and the feed-health gates (constituents healthy, book depth, sigma range).

## Invalidation / exit condition
When the thesis is dead. Includes: the edge closing, the model flipping sides,
feed/book health degrading, and the near_close phase cutoff (no skill holds into
settlement noise). An entry without an invalidation is not a skill.

## Position sizing
Expressed in bankroll-% terms and Kelly-fraction terms. References the central
risk-management parameters — never hardcodes its own bankroll math beyond a per-skill
cap or multiplier. Draft-status skills are additionally bound by
MAX_CONTRACTS_PER_WINDOW.

## Edge cases / do-not-trade list
Known situations where the entry condition fires but the trade is bad (thin
overnight/weekend books, vol regime breaks, constituent dropouts, settlement-window
gamma).

## Data dependencies
Which skills this reads from (crypto-price-feed, kalshi-ws-orderbook,
window-monitor, fair-value-model, kalshi-client, risk-management).
