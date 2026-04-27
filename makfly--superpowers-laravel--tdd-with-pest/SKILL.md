---
name: laraveltdd-with-pest
description: Deliver Laravel behavior safely with test-first workflows and deterministic regression protection. Use for tdd with pest tasks. Use when this capability is needed.
metadata:
  author: makfly
---

# Tdd With Pest (Laravel)

## Use when
- Implementing new behavior with regression risk.
- Fixing bugs via reproducible failing tests.

## Default workflow
1. Write a failing test for expected behavior and one edge case.
2. Implement minimal code to pass.
2. Refactor for clarity while keeping tests green.
2. Run targeted tests, then broader suite for impacted modules.

## Guardrails
- No behavior change without test evidence.
- Prefer deterministic fixtures and isolated state.
- Assert business outcomes instead of internals.

## Progressive disclosure
- Start with this file for execution posture and constraints.
- Load references only for deep implementation detail or edge cases.

## Output contract
- RED/GREEN/REFACTOR summary.
- Test files changed and command output.
- Remaining coverage gaps.

## References
- `reference.md`
- `docs/complexity-tiers.md`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/makfly) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
