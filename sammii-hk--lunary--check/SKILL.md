---
name: check
description: Run quick quality checks (lint + unit tests) Use when this capability is needed.
metadata:
  author: sammii-hk
---

# Quick Check

Run the quick quality check script which includes linting and unit tests.

## Usage

When invoked, run:

```bash
pnpm check:quick
```

This runs:

1. `pnpm lint` - ESLint with auto-fix
2. `pnpm test` - Jest unit tests

Report any failures with details so the user can address them.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sammii-hk) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
