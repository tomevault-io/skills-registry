---
name: check-all
description: Run full quality checks (lint + tests + e2e smoke + build) Use when this capability is needed.
metadata:
  author: sammii-hk
---

# Full Check

Run the comprehensive quality check script which includes linting, tests, e2e smoke tests, and a production build.

## Usage

When invoked, run:

```bash
pnpm check:all
```

This runs:

1. `pnpm lint` - ESLint with auto-fix
2. `pnpm test` - Jest unit tests
3. `pnpm test:e2e:smoke` - Playwright smoke tests
4. `pnpm build` - Next.js production build

This is a thorough check suitable for pre-commit or pre-PR validation.

Report any failures with details so the user can address them.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sammii-hk) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
