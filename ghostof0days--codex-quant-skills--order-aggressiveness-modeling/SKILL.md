---
name: order-aggressiveness-modeling
description: Order aggressiveness modeling workflows for selecting passive-to-aggressive placement under queue and alpha-decay constraints. use when tasks involve aggressiveness policy design, queue-jump decisions, and execution-probability tradeoffs. Use when this capability is needed.
metadata:
  author: ghostof0days
---

# Order Aggressiveness Modeling
## objective
Model and calibrate order aggressiveness to maximize fill quality while controlling information leakage.

## workflow
1. define aggressiveness states and queue-priority assumptions.
2. estimate fill probability and adverse-selection by aggressiveness level.
3. model alpha decay versus waiting cost for passive orders.
4. optimize aggressiveness policy by market state and urgency.
5. deploy only when policy improves net execution utility.

## required diagnostics
- fill probability by aggressiveness bucket and venue.
- adverse-selection cost conditional on aggressive fills.
- queue-wait opportunity cost under alpha decay.
- policy regret versus ex-post best aggressiveness.
- stability of aggressiveness transitions across regimes.

## risk controls
- enforce maximum aggressive participation under stress.
- enforce anti-gaming checks for quote stuffing conditions.
- enforce conservative fallback when queue metrics are stale.

## outputs
- run `python scripts/order_aggressiveness_modeling_diagnostics.py input.csv --output diagnostics.json` and keep the json artifact.
- write an implementation memo using `references/order-aggressiveness-modeling-playbook.md` with assumptions, tests, limits, and rollout plan.

## resources
- use `scripts/order_aggressiveness_modeling_diagnostics.py` for deterministic diagnostics.
- use `references/order-aggressiveness-modeling-playbook.md` for the domain checklist and delivery structure.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ghostof0days) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
