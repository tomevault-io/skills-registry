---
name: frontend-dev-guidelines
description: Frontend development guidelines for React/TypeScript (components, data fetching, routing, styling, performance). Keywords: frontend, react, typescript, ui, components. Use when this capability is needed.
metadata:
  author: willyu1007
---

# Frontend Development Guidelines (React / TypeScript)

This skill provides reusable guidance for building modern frontend applications with React and TypeScript.

---

## Purpose

- Keep UI code consistent, predictable, and testable.
- Avoid common UX regressions (layout shift, inconsistent loading states).
- Improve performance via code splitting and memoization patterns.
- Keep type safety high and `any` usage low.

---

## When to Use

Use this skill when:
- Creating components, pages, or UI features
- Adding client-side data fetching and caching
- Working on routing/navigation
- Styling components and defining UI patterns
- Optimizing rendering performance
- Defining TypeScript standards for the frontend

---

## Quick Start (high-signal checklist)

### New component

- [ ] Define a clear `Props` type/interface
- [ ] Keep render pure; move side effects into hooks
- [ ] Use memoization (`useMemo` / `useCallback`) only when it matters
- [ ] Prefer consistent loading/error UX (Suspense or explicit states)
- [ ] Ensure accessibility basics (labels, focus order, keyboard support)

### New feature

- [ ] Create a feature folder with a small public surface (`index.ts`)
- [ ] Separate API/data access, UI components, hooks, and types
- [ ] Add at least one integration test for the critical user flow (if test harness exists)

---

## Related Skills

| Need to… | Skill |
|---------|------|
| Component structure patterns | `component-patterns` |
| Data fetching and caching | `data-fetching` |
| Organize code and feature folders | `file-organization` |
| Loading and error states | `loading-and-error-states` |
| Routing patterns | `routing-guide` |
| Styling guidelines | `styling-guide` |
| Performance optimization | `performance` |
| TypeScript standards | `typescript-standards` |
| Common UI patterns | `common-patterns` |
| Fix frontend errors | `frontend-error-fixer` |
| End-to-end examples | `complete-examples` |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/willyu1007) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
