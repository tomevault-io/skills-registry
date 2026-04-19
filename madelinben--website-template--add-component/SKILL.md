---
name: add-component
description: Add a new design system component following constitution patterns. Use when creating Modal, UserCard, or any primitive/block in packages/design. Use when this capability is needed.
metadata:
  author: madelinben
---

Add a new design system component to `packages/design` following project constitution.

**Component structure** (per constitution):
- ComponentName.tsx – main UI
- ComponentName.skeleton.tsx – skeleton variant
- ComponentName.error.tsx – error variant (when applicable)
- index.ts – exports
- Add design-system.css variants if needed

**Rules**:
- Generic only: no API/SDK imports; standard TypeScript and React types
- Props: string, number, boolean, ReactNode, ReactElement, Record<string, unknown>
- Tailwind only; use design-system.css tokens (btn, card, input-base, skeleton)
- British English, full words

**Component to add**: $ARGUMENTS

1. Create the component directory under primitives/, components/, blocks/, or layout/
2. Implement main component, skeleton, error (if needed), index
3. Export from packages/design/src/index.ts
4. Add a Storybook story in apps/storybook with skeleton and error variants

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/madelinben) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
