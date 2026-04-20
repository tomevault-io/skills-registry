---
name: liftera-ui
description: > Use when this capability is needed.
metadata:
  author: estebandrg
---

## Source of Truth (REQUIRED)

- **ALWAYS** treat `packages/ui` as the source of truth for reusable UI.
- **NEVER** duplicate components across `apps/web` and `apps/mobile`.

## Component Placement (REQUIRED)

- **ALWAYS** put Gluestack primitives/wrappers under `packages/ui/src/gluestack/*`.
- **ALWAYS** put project components under `packages/ui/src/components/*`.
- **ALWAYS** export components through stable entrypoints (avoid deep imports across apps/packages).

## Cross-Platform Constraints (REQUIRED)

- **ALWAYS** ensure components work in both React Native and React Native Web unless explicitly documented.
- **NEVER** use DOM-only APIs inside shared UI components.
- **ALWAYS** use platform files when needed (`*.native.tsx`, `*.web.tsx`).

## Styling Rules

- **ALWAYS** use NativeWind/Tailwind utility classes consistently.
- **ALWAYS** keep variants predictable (small set of variants, explicit defaults).

## Imports & Usage

- **ALWAYS** import shared components via the package name (example from README):
  - `@acme/ui/gluestack/*`
  - `@acme/ui/components/*`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/estebandrg) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
