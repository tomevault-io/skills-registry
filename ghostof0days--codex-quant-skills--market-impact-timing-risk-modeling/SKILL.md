---
name: market-impact-timing-risk-modeling
description: Market impact and timing risk workflows for trading cost decomposition, execution horizon design, and urgency optimization. use when tasks involve separating impact versus timing risk and selecting cost-risk tradeoffs. Use when this capability is needed.
metadata:
  author: ghostof0days
---

# Market Impact Timing Risk Modeling
## objective
Model market impact and timing risk to optimize execution horizon and urgency settings.

## workflow
1. define execution horizon candidates and risk aversion parameters.
2. estimate temporary and permanent impact components from trade data.
3. estimate timing-risk distribution across horizon alternatives.
4. compute efficient cost-risk frontier for strategy selection.
5. deploy chosen horizon only after out-of-sample stability checks.

## required diagnostics
- impact coefficient stability by venue and volatility regime.
- timing-risk tail behavior across horizon choices.
- cost-risk frontier drift over rolling windows.
- forecast error between expected and realized implementation shortfall.
- capacity sensitivity under order-size scaling.

## risk controls
- enforce horizon limits for high-volatility sessions.
- enforce recalibration cadence for impact parameters.
- enforce fallback urgency profile on model instability.

## outputs
- run `python scripts/market_impact_timing_risk_modeling_diagnostics.py input.csv --output diagnostics.json` and keep the json artifact.
- write an implementation memo using `references/market-impact-timing-risk-modeling-playbook.md` with assumptions, tests, limits, and rollout plan.

## resources
- use `scripts/market_impact_timing_risk_modeling_diagnostics.py` for deterministic diagnostics.
- use `references/market-impact-timing-risk-modeling-playbook.md` for the domain checklist and delivery structure.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ghostof0days) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
