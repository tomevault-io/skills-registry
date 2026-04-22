---
name: financial-computing-python
description: Python financial computing workflows for pandas-based research pipelines, event-driven backtesting prototypes, and production-safe quant utilities. use when tasks involve building or validating Python tooling for market data, signals, and strategy analytics. Use when this capability is needed.
metadata:
  author: ghostof0days
---

# Financial Computing Python
## objective
Build Python tooling for quant workflows with reproducible data transformations and testable research code.

## workflow
1. define data contracts, calendar conventions, and feature definitions before coding.
2. implement pandas pipelines for ingestion, cleaning, and time alignment.
3. build reusable modules for signals, portfolio logic, and analytics.
4. validate outputs with deterministic unit tests and replay datasets.
5. promote only after profiling runtime and memory behavior on production-size data.

## required diagnostics
- feature leakage and look-ahead checks on timestamp alignment.
- missing-data handling consistency across symbols and sessions.
- run-time and memory cost by pipeline stage.
- signal reproducibility across repeated runs and seeds.
- backtest consistency between batch and incremental updates.

## risk controls
- enforce schema validation at ingestion boundaries.
- enforce test coverage for pricing, returns, and pnl calculations.
- enforce rollback to previous release on reproducibility failures.

## outputs
- run `python scripts/financial_computing_python_diagnostics.py input.csv --output diagnostics.json` and keep the json artifact.
- write an implementation memo using `references/financial-computing-python-playbook.md` with assumptions, tests, limits, and rollout plan.

## resources
- use `scripts/financial_computing_python_diagnostics.py` for deterministic diagnostics.
- use `references/financial-computing-python-playbook.md` for the domain checklist and delivery structure.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ghostof0days) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
