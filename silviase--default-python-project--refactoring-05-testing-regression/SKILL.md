---
name: refactoring-05-testing-regression
description: Use when adding tests and regression checks for Python research code.
metadata:
  author: silviase
---

# Refactoring 05: Testing and Regression

## Goal

Protect core logic with small, fast tests and regression checks for key metrics.

## Sequence

- Order: 05
- Previous: refactoring-04-data-io-validation
- Next: refactoring-06-static-analysis-style

## Workflow

- Identify stable, critical functions and write unit tests first.
  - Success: Core logic has focused unit tests.
- Add regression tests for metrics or outputs using small fixtures.
  - Success: Key metrics have stable regression checks.
- Keep tests fast; mark slow or data heavy tests as optional.
  - Success: Default test run finishes quickly.
- Prefer deterministic fixtures over sampling from large datasets.
  - Success: Tests run deterministically without external data.
- Add a single command to run tests via `uv run`.
  - Success: One documented command runs the full test suite.

## Guardrails

- Avoid testing training loops end to end unless requested.
- Do not add flaky tests; stabilize randomness before asserting values.
- Keep test data small and stored under `tests/`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/silviase) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
