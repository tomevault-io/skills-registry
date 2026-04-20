---
name: turborepo
description: > Use when this capability is needed.
metadata:
  author: estebandrg
---

## Core Commands (REQUIRED)

- Root scripts are Turborepo-driven:
  - `pnpm dev` -> `turbo run dev`
  - `pnpm build` -> `turbo run build`
  - `pnpm lint` -> `turbo run lint`
  - `pnpm check-types` -> `turbo run check-types`

## Task Principles (REQUIRED)

- **ALWAYS** define tasks in `turbo.json`.
- **ALWAYS** keep `dev` non-cached and persistent.
- **ALWAYS** keep build outputs accurate for caching.

## When Editing turbo.json

- Ensure task dependencies include `^task` when packages depend on other packages.
- Keep inputs/outputs minimal and correct so caching is reliable.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/estebandrg) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
