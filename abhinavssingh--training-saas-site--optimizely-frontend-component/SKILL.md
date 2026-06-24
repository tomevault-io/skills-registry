---
name: optimizely-frontend-component
description: Conventions for building accessible, RTL-ready, modular layout/UI components in this Next.js 16 + React 19 + Tailwind 4 + Headless UI 2 codebase. Use this skill whenever the user asks to: (1) build, refactor, or extend any component under src/components/layout/** (Header, Footer, Nav, Sidebar, Drawer); (2) add interactive UI primitives like dropdowns, mega menus, dialogs, popovers, disclosures, command palettes, language switchers, mobile menus; (3) make existing UI keyboard-accessible or RTL-safe; (4) introduce a new family of UI components (cards, banners, ctas) where the modular file layout, data colocation, and a11y/RTL principles below apply. This skill captures the patterns used in src/components/layout/Header/ and src/components/layout/Footer/ — the canonical references. Use when this capability is needed.
metadata:
  author: abhinavssingh
---

# Optimizely Frontend Component Conventions

Principles for building UI components in this codebase. The Header at `src/components/blocks/Header/` and Footer at `src/components/blocks/Footer/` are the canonical reference implementations — when in doubt, mirror their structure.

## When to apply this skill

Apply to anything that renders interactive UI:

- Layout chrome — `Header`, `Footer`, `Sidebar`, `Drawer`, breadcrumbs.
- Interactive primitives — dropdowns, mega menus, popovers, disclosures, dialogs, tooltips, tabs, listboxes, comboboxes, language switchers, mobile menus.
- Composable visual blocks where children may be added later (cards, banners, CTAs) and the structure should stay open for extension.

Do **not** apply this skill to pure CMS content type definitions (use `optimizely-cms-content-types`) or deployment scripts (use `optimizely-frontend-hosting`).

## Core principles

Each principle has a topical reference under `references/` with the full detail.

1. **Modular file layout.** One concern per file. Subcomponents live next to their parent in a feature folder. Data is colocated. → `references/file-layout.md`

2. **Headless UI by default.** Any interactive primitive that has a Headless UI equivalent uses it. Don't roll bespoke open/close state machines. The v2 specifics (when to use `transition` prop, `anchor` instead of hand-positioning, the click-outside bug) matter. → `references/headless-ui.md`

3. **Accessibility is part of "done."** Native semantics first, visible focus rings on every interactive element, ARIA wired correctly on toggles, list semantics on lists. → `references/accessibility.md`

4. **RTL by default.** Use logical CSS properties so `<html dir="rtl">` flips the layout for free. Never hand-write `dir="rtl"` on individual elements. → `references/rtl.md`

5. **Open-for-extension data shapes.** Schemas should let new entries (children, items, sections) render correctly with zero component edits. The mega menu pattern is the canonical example. → `references/mega-menu-pattern.md`

6. **Server-first, client when needed.** Mark a file `"use client"` only when it actually uses hooks, refs, event handlers, or browser APIs. → `references/file-layout.md` (Server vs client section)

7. **Keep visual styling out of CMS content types.** Visual variants belong in display templates (see `optimizely-cms-content-types`), not in component prop enums.

8. **High-level architecture and patterns.** This skill captures the high-level architecture and patterns for layout components, including the rationale for the principles above and how they work together to create a scalable, maintainable codebase. → `references/layout-architecture.md`

9. **Content resolution.** — how content resolution works for layout components, including the use of `getContent` and `getLink` helpers, the `ContentResolver` component, and best practices for structuring content types to work well with these patterns. → `references/content-resolution.md`

## Quick start

For a typical new layout component (a drawer, a sidebar, a mega menu, etc.):

1. Read `references/file-layout.md` for the folder structure and TypeScript conventions.
2. Read `references/headless-ui.md` to pick the right primitive and avoid the v2 pitfalls.
3. Read `references/accessibility.md` and `references/rtl.md` to know what "done" looks like.
4. Look at `references/header-reference.md` (CMS driven) and `references/footer-reference.md` (CMS driven) for annotated walkthroughs of how each principle is applied.
5. If something breaks, check `references/troubleshooting.md` first — most frontend bugs in this codebase have already been encountered and documented.

## Authoring checklist

Before considering any layout/UI component done:

1. File layout follows `src/components/layout/<Feature>/` with one concern per file;
2. Every interactive primitive uses Headless UI; no hand-rolled open/close state for things HUI covers.
3. `npm run lint` is clean; `npx tsc --noEmit` is clean.
4. Tab through every interactive surface — focus is always visible, Esc closes overlays, click-outside closes overlays.
5. Set `<html dir="rtl">` in DevTools and confirm the layout mirrors with no overlap, no off-canvas, and direction-implying icons (arrows) flip while neutral icons (chevrons-down) stay.
6. At the `lg` breakpoint boundary, desktop and mobile UIs are mutually exclusive.
7. Adding a new entry to the data file renders correctly with zero component edits.

## References

- `references/file-layout.md` — Folder structure, data colocation, server-vs-client split, naming, TypeScript conventions.
- `references/headless-ui.md` — Primitive lookup table, v2 specifics, annotated examples for Popover and Disclosure.
- `references/accessibility.md` — A11y checklist with the rationale and code patterns for each item.
- `references/rtl.md` — RTL principles, logical-property mapping, `useDocumentLanguage` hook, what to flip and what not to.
- `references/mega-menu-pattern.md` — Schema and adaptive-layout rules; how to add a new category with zero component edits.
- `references/header-reference.md` — Annotated walkthrough of the Header (state-owning shell, Headless UI, RTL toggle, mega menu, mobile drawer); the Header refactor fix log.
- `references/footer-reference.md` — Annotated walkthrough of the Footer (server-rendered, content-info landmark, link grid, social icons); the Footer audit fix log; responsive-grid defaults.
- `references/troubleshooting.md` — Click-outside doesn't close, Tab stops invisible, span-as-link, RTL doesn't mirror, hydration warnings, file-truncation recovery.
- `references/search-reference` — Annotated walkthrough of the Search

---
> Source: [abhinavssingh/training-saas-site](https://github.com/abhinavssingh/training-saas-site) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-22 -->
