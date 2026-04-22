---
name: pest-testing
description: >- Use when this capability is needed.
metadata:
  author: erinrugas
---

# Testing Skill

## When to Apply
- User asks to add or fix tests.
- Changes require validation coverage.

## Workflow
1. Read test strategy from `specs/qa-spec.md` and `specs/specs.md`.
2. Detect test framework from repo (Pest, PHPUnit, Jest, Vitest, Playwright, etc.).
3. Add minimal high-value tests:
   - happy path
   - validation/edge path
   - auth/permission path when applicable
4. Run the smallest relevant test subset first, then broader suite if needed.

## Quality Bar
- Tests are deterministic.
- Assertions verify behavior, not implementation detail.
- New tests fail without the fix and pass with it.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/erinrugas) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
