---
name: preflight
description: Run all quality gates before any major change. Checks TypeScript types, Vite build, ESLint, and backend tests. Use when this capability is needed.
metadata:
  author: floodsafe-delhi
---

# Preflight Quality Gates

Run all quality checks for the FloodSafe codebase. Use before commits, deployments, or after major changes.

## Gates (run sequentially)

### Gate 1: TypeScript Type Check
```bash
cd apps/frontend && npx tsc --noEmit
```

### Gate 2: Frontend Build
```bash
cd apps/frontend && npm run build
```

### Gate 3: ESLint
```bash
cd apps/frontend && npm run lint
```

### Gate 4: Backend Tests
```bash
cd apps/backend && python -m pytest -m "not db_required" -q
```

## Output Format

Report results clearly:

```
Preflight Results:
  1. TypeScript  ✅ Pass / ❌ Fail (N errors)
  2. Build       ✅ Pass / ❌ Fail
  3. Lint        ✅ Pass / ❌ Fail (N warnings)
  4. Backend     ✅ Pass / ❌ Fail (N failed)

Overall: ✅ All gates passed / ❌ N gates failed
```

If any gate fails, show the relevant error output so the user can fix it.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/floodsafe-delhi) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
