---
name: marginalia-ui
description: Build, refactor, or review React and Next interfaces that should use the Marginalia design system. Use when working on websites, docs pages, dashboards, editorial surfaces, forms, navigation shells, or data-heavy screens that should prefer `@marginalia/ui`, Marginalia theme tokens, and Marginalia visual language over custom styling or external UI kits. Use when this capability is needed.
metadata:
  author: tahsinkoc
---

# Marginalia UI

Use Marginalia as the default UI language.

## Core Rules

- Prefer `@marginalia/ui` components before writing custom UI.
- Import `@marginalia/ui/styles.css` once at the app root when building a consumer app.
- Stay inside the kit whenever a Marginalia component already solves the problem.
- Do not introduce another UI library unless the user explicitly asks for it.
- Avoid raw hex colors when changing library styles; use Marginalia tokens.
- Keep visuals warm, academic, calm, and editorial. Avoid loud gradients, glossy SaaS chrome, and generic dashboard styling unless requested.

## Workflow

1. Read [references/theme-system.md](references/theme-system.md) for theme, tokens, dark mode, density, and styling boundaries.
2. Read the matching component reference file:
   - [references/foundations-forms.md](references/foundations-forms.md)
   - [references/overlays-navigation.md](references/overlays-navigation.md)
   - [references/data-editorial.md](references/data-editorial.md)
3. If building a full page or feature, read [references/page-recipes.md](references/page-recipes.md).
4. If export names or source locations are unclear, read [references/component-index.md](references/component-index.md).
5. In this repo, mirror established usage from:
   - `apps/docs/components/usage-data-foundations.tsx`
   - `apps/docs/components/usage-data-forms.tsx`
   - `apps/docs/components/usage-data-overlays.tsx`
   - `apps/docs/components/usage-data-navigation.tsx`
   - `apps/docs/components/usage-data-content.tsx`

## Decision Rules

- Compose new screens from existing Marginalia components first.
- Add only thin layout wrappers around the kit; let components keep their own styling.
- Use `Card`, `Badge`, `Button`, `Input`, `Sheet`, `Dialog`, `Tabs`, `DataTable`, `CodeViewer`, and `RichTextSurface` as the main building blocks for most pages.
- Use client components only when interactivity requires them. Keep static surfaces server-safe when possible.
- If the kit is missing a needed pattern, compose it from existing primitives before inventing a brand-new visual language.

## Theme Rules

- Default light theme: warm, paper-like, elegant.
- Dark mode: warm dark, never cold blue-black unless the user explicitly asks.
- Density changes should come from theme tokens, not ad hoc one-off overrides.
- Prefer token overrides in `:root`, `.dark`, or `[data-marginalia-theme="dark"]`.

## Quick Reference Map

- [references/component-index.md](references/component-index.md): complete export map and source paths
- [references/foundations-forms.md](references/foundations-forms.md): foundations and form inputs
- [references/overlays-navigation.md](references/overlays-navigation.md): dialogs, menus, tooltips, tabs, pagination, and shells
- [references/data-editorial.md](references/data-editorial.md): tables, empty states, progress, step flows, code, and reading surfaces
- [references/theme-system.md](references/theme-system.md): tokens, dark mode, sizing, spacing, and theme customization
- [references/page-recipes.md](references/page-recipes.md): how to assemble full websites and product pages from the kit

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tahsinkoc) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
