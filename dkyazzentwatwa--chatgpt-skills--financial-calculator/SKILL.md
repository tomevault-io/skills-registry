---
name: financial-calculator
description: Run loan, investment, NPV, retirement, savings, and risk calculations with schedules and charts. Use for deterministic financial modeling tasks. Use when this capability is needed.
metadata:
  author: dkyazzentwatwa
---

# Financial Calculator

Use deterministic calculations instead of ad hoc spreadsheet math when the user needs precise financial outputs.

## Use This For

- Loan and mortgage math
- Investment growth and savings goals
- NPV, IRR, and payback analysis
- Retirement projections and withdrawal planning
- Monte Carlo style risk scenarios

## Workflow

1. Confirm units and assumptions first: rates, compounding, time horizon, taxes, and inflation.
2. Use `scripts/financial_calc.py` as the source of truth for the computation.
3. Return both the answer and the assumptions that materially drive it.

## Guardrails

- Treat outputs as calculations, not personalized financial advice.
- Surface simplifying assumptions and scenario sensitivity.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/dkyazzentwatwa) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
