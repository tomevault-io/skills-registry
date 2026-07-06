---
name: nostrstack-embed-ui
description: Nostrstack embed UI and design system guidance for @nostrstack/embed, @nostrstack/blog-kit, and apps/gallery. Use when editing widgets, tokens, CSS classes, or gallery UI behavior. Use when this capability is needed.
metadata:
  author: majiayu000
---

# Embed UI + Design System

Use this skill for UI work in `packages/embed`, `packages/blog-kit`, and `apps/gallery`.

## Workflow

- Read `references/design-system.md` for tokens, CSS variables, and component classes.
- Use `references/ui-specs.md` for specific UI specs (event landing, zap UI, QR, etc.).
- Verify UI changes with Chrome DevTools MCP (or QA fallback per dev workflow).

## Guardrails

- Prefer design tokens and `.nostrstack-*` primitives over hard-coded colors.
- Keep motion within defined durations/easings and respect reduced motion.
- Gallery should act as the canary for embed UI regressions.

---
> Source: [majiayu000/claude-skill-registry](https://github.com/majiayu000/claude-skill-registry) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-07-06 -->
