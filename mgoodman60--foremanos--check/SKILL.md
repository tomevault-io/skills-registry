---
name: check
description: Quick health check (lint, types, tests) Use when this capability is needed.
metadata:
  author: mgoodman60
---

Run a quick health check on the codebase.

## Steps

1. Run TypeScript type check
2. Run ESLint
3. Run smoke tests
4. Report results

## Commands

```bash
# Type check
npx tsc --noEmit

# Lint
npm run lint

# Quick tests
npm test -- __tests__/smoke --run
```

## Output Format

```markdown
## Health Check

### TypeScript
- Status: ✓ Pass / ✗ Fail
- Errors: X

### ESLint
- Status: ✓ Pass / ✗ Fail
- Warnings: X
- Errors: X

### Smoke Tests
- Status: ✓ Pass / ✗ Fail
- Passed: X
- Failed: X

### Overall: [Healthy/Issues Found]
```

## Quick Fixes

| Issue | Command |
|-------|---------|
| Lint auto-fix | `npm run lint -- --fix` |
| Prisma types | `npx prisma generate` |
| Missing deps | `npm install` |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mgoodman60) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
