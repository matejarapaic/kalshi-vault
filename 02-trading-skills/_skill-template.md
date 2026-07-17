---
skill: <name>
sports: [all]  # or scoped: [nfl, nba, mlb]
market_conditions: []  # tags the skill-matcher filters on, e.g. [live, pregame, high-volatility, blowout, final-minutes]
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
it should exist at all.

## Entry condition
Precise and mechanical. Numbers, not vibes. Every quantity must name its data source
(which skill produces it). If a human couldn't hand-check the condition against a
screenshot of the data, it's not specific enough.

## Invalidation / exit condition
When the thesis is dead. Includes: the edge closing, contrary information arriving,
and time/game-state cutoffs. An entry without an invalidation is not a skill.

## Position sizing
Expressed in bankroll-% terms and Kelly-fraction terms. References the central
risk-management parameters — never hardcodes its own bankroll math beyond a per-skill
cap or multiplier.

## Edge cases / do-not-trade list
Known situations where the entry condition fires but the trade is bad.

## Data dependencies
Which skills this reads from (espn-data, odds-api, kalshi-client, etc.).
