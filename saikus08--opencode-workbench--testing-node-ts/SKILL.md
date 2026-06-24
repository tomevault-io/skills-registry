---
name: testing-node-ts
description: Write and validate Node/TypeScript tests (unit/integration/e2e) Use when this capability is needed.
metadata:
  author: saikus08
---

# Testing Node/TS

## Process

1. Identify the behavior change (expected inputs/outputs).
2. Choose the right level: unit vs integration vs e2e.
3. Add edge cases and regression coverage.
4. Run `npm test` and fix failures.

## Notes

- Prefer deterministic tests.
- Avoid mocking internals unless necessary.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/saikus08) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
