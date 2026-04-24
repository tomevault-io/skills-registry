---
name: markdowntown-frontend
description: Frontend UX and design system guidance for markdowntown. Use when changing UI, layout, styling, typography, motion, or Tailwind classes, especially for Workbench, Atlas, and Library surfaces. Use when this capability is needed.
metadata:
  author: joelklabo
---

# markdowntown-frontend

## Core workflow
1. Identify the surface (Home, Atlas, Workbench, Library, Detail, Translate).
2. Check design constraints and lint rules before editing styles.
3. Apply changes with existing component patterns and tokens.
4. Validate responsive layout and run lint/test commands.

## Guardrails
- Avoid raw hex colors (lint: `pnpm lint:hex`).
- Avoid Tailwind neutral palette (lint: `pnpm lint:neutrals`).
- Prefer existing spacing/typography tokens and component primitives.
- Keep motion subtle and aligned with UX guidance.

## References
- docs/DESIGN_SYSTEM.md
- docs/design/motion-responsive.md
- docs/design/sitewide-ui-audit.md
- docs/ux/primary-flow-spec.md
- codex/skills/markdowntown-frontend/references/design-system.md
- codex/skills/markdowntown-frontend/references/ui-ux.md

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/joelklabo) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
