---
name: typecheck
description: Run TypeScript type checking on the codebase Use when this capability is needed.
metadata:
  author: sammii-hk
---

# TypeScript Check

Run the TypeScript compiler to check for type errors without emitting files.

## Usage

When invoked, run:

```bash
pnpm exec tsc --noEmit
```

This will check all TypeScript files for type errors without producing output files.

Report any type errors found with their file paths and line numbers.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sammii-hk) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
