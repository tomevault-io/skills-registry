---
name: run-quality-gates
description: Run lint, typecheck, and tests (same as pre-commit hook) Use when this capability is needed.
metadata:
  author: jhosm
---

# Run Quality Gates

Run the full quality gate suite (matches `.githooks/pre-commit`):

1. `npm run lint`
2. `npx tsc --noEmit`
3. `npm test`

Report pass/fail for each step. If any fail, show the error output.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jhosm) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
