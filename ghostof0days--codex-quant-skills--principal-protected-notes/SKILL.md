---
name: principal-protected-notes
description: Principal Protected Notes workflows for structured payoff decomposition, participation design, and issuer-risk-aware hedging. use when tasks involve principal-protected structures, embedded option packaging, funding assumptions, client payoff design, or production risk monitoring for capital-protected products. Use when this capability is needed.
metadata:
  author: ghostof0days
---

# Principal Protected Notes
## objective
Execute principal-protected note workflows with transparent payoff decomposition, hedge diagnostics, and robust control gates.

## workflow
1. define payoff terms, participation, barriers, maturity, and protection constraints.
2. decompose note value into bond floor, option sleeve, fees, and funding spread components.
3. calibrate option assumptions and hedge exposures across delta, gamma, vega, and carry.
4. stress issuer spread, volatility shocks, and liquidity slippage on projected outcomes.
5. release only when payout logic, pricing error, and hedge risk pass control limits.

## required diagnostics
- bond-floor and option-cost decomposition consistency.
- participation-rate sensitivity to volatility and funding assumptions.
- greek exposure concentration and hedge slippage behavior.
- issuer-spread widening impact on note value and protection quality.
- payout replication error across stressed paths.

## risk controls
- enforce pricing-error and hedge-slippage hard thresholds.
- enforce issuer concentration and spread-sensitivity limits.
- enforce fallback assumptions for illiquid strikes and tenors.

## outputs
- run `python scripts/principal_protected_notes_diagnostics.py input.csv --output diagnostics.json` and keep the json artifact.
- write an implementation memo using `references/principal-protected-notes-playbook.md` with assumptions, tests, limits, and rollout plan.

## resources
- use `scripts/principal_protected_notes_diagnostics.py` for deterministic diagnostics.
- use `references/principal-protected-notes-playbook.md` for the domain checklist and delivery structure.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ghostof0days) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
