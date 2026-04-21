---
name: lang-ts
description: TypeScript/JavaScript conventions and reliability checklist for agents (build, typecheck, tests). Use when this capability is needed.
metadata:
  author: t3chn
---

# TypeScript / JavaScript Conventions

- Prefer the project’s package manager lockfile (`pnpm`/`yarn`/`npm`).
- Run `npm|pnpm|yarn` scripts for build/test/lint; keep CI green.
- If TypeScript is present, ensure `tsc` (or equivalent) passes.
- Avoid introducing new deps unless necessary; document why.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/t3chn) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
