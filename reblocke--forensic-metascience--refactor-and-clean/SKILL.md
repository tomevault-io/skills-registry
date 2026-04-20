---
name: refactor-and-clean
description: Safely refactor code: identify dead code, improve naming, reduce duplication, run format/lint, and protect behavior with tests. Use when this capability is needed.
metadata:
  author: reblocke
---

## Procedure
1. Identify the refactoring goal (clarity, modularity, performance, API stability).
2. Ensure tests exist for current behavior (add if missing).
3. Refactor in small steps; run tests after each step.
4. Remove dead code (trust git history).
5. Run `ruff` and ensure the pipeline still runs.
6. Update docs if interfaces change.

## Guardrails
- Do not change scientific behavior unless requested.
- If behavior must change, document the change and update tests.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/reblocke) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
