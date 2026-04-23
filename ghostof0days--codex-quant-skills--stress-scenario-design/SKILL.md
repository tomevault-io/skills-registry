---
name: stress-scenario-design
description: Stress Scenario Design workflows for quantitative research, implementation, and production controls. use when tasks involve extreme-scenario coverage and capital-impact assessment. Use when this capability is needed.
metadata:
  author: ghostof0days
---

# Stress Scenario Design
## objective
Execute stress scenario design work with reproducible research, explicit controls, and deployable outputs.

## workflow
1. define risk appetite, limit hierarchy, and escalation rules.
2. aggregate exposures across products, venues, and legal entities.
3. measure pnl, tail risk, and scenario outcomes with daily replay.
4. investigate breaches with root-cause attribution and remediation plans.
5. approve production only with auditable controls and rollback procedures.

## required diagnostics
- limit-breach frequency and concentration by strategy and desk.
- tail-risk evolution across volatility and liquidity regimes.
- scenario-loss decomposition by factor and instrument class.
- control effectiveness and incident-response latency.

## risk controls
- enforce hard and soft limits with automated blocking paths.
- enforce intraday breach escalation and documented owner actions.
- enforce independent model and control validation cadences.

## outputs
- run `python scripts/stress_scenario_design_diagnostics.py input.csv --output diagnostics.json` and keep the json artifact.
- write an implementation memo using `references/stress-scenario-design-playbook.md` with assumptions, tests, limits, and rollout plan.

## resources
- use `scripts/stress_scenario_design_diagnostics.py` for deterministic diagnostics.
- use `references/stress-scenario-design-playbook.md` for the domain-specific checklist and delivery structure.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ghostof0days) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
