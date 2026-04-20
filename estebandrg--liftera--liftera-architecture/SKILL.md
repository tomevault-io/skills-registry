---
name: liftera-architecture
description: > Use when this capability is needed.
metadata:
  author: estebandrg
---

## Monorepo Boundaries (REQUIRED)

- **ALWAYS** keep application code inside `apps/*`.
- **ALWAYS** keep reusable code inside `packages/*`.
- **NEVER** import app code from another app (e.g. `apps/web` importing from `apps/mobile`). Extract shared logic into a package.

## Package Design (REQUIRED)

- **ALWAYS** design packages as platform-agnostic when possible.
- **ALWAYS** expose stable entrypoints from each package (avoid deep imports across packages).
- **NEVER** duplicate shared UI or utilities across apps; add/extend a package instead.

## Cross-Platform UI Rules (REQUIRED)

- **ALWAYS** prefer shared UI in `packages/ui`.
- **ALWAYS** keep platform-specific implementations behind explicit entrypoints (e.g. `*.native.tsx`, `*.web.tsx`) when needed.
- **NEVER** assume DOM APIs exist in mobile code.

## Dependency & Ownership Rules

- **ALWAYS** keep dependencies as low as possible in packages.
- **ALWAYS** add dependencies at the narrowest scope that needs them (package/app `package.json`, not the root).
- **NEVER** add a dependency to the root unless it is truly repo-wide tooling.

## Repo Structure Quick Reference

- `apps/web`: Next.js app
- `apps/mobile`: Expo app
- `packages/ui`: shared UI (Gluestack UI + shared components)
- `turbo.json`: task orchestration
- `pnpm-workspace.yaml`: workspace boundaries

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/estebandrg) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
