---
name: new-component
description: Scaffold a new React component matching mta-website conventions Use when this capability is needed.
metadata:
  author: itayost
---

Create a new component: $ARGUMENTS

## Conventions to follow:
1. Read existing components in the target directory for patterns
2. Use `cn()` from `@/lib/utils` for conditional classes
3. Define TypeScript interface for props above the component
4. Use named export (not default)
5. Use logical CSS properties (start/end, never left/right)
6. All UI text in Hebrew
7. Use oklch design tokens from globals.css (primary, accent, neutral, success, error)

## Placement:
- UI primitives → `components/ui/`
- Layout components → `components/layout/`
- Page sections → `components/sections/`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/itayost) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
