---
name: atomic-template
description: Create a React TEMPLATE that defines layout and composition without binding to real data sources. Use for page shells, dashboards, list-detail layouts, etc. Templates wire organisms together and expose slots/regions. Use when this capability is needed.
metadata:
  author: andireuter
---

# Atomic Design: TEMPLATE (React Functional Components)

You are creating a **TEMPLATE**: page-level layout composition without real data binding.

## Hard rules

1. **Functional components only**.
2. **No real data fetching** in templates. Templates define structure, regions, and composition.
3. **Slot-based design**:
   - Prefer `header`, `sidebar`, `content`, `footer` regions via props/children.
   - Avoid hard-coding copy and domain-specific assumptions.
4. **Responsiveness & semantics**:
   - Use proper landmarks and heading structure.
   - Ensure layout works across breakpoints (as dictated by project conventions).

## Output expectations

- Provide:
  1. `Template.tsx`
  2. `Template.stories.tsx` showing multiple compositions
  3. Optional `Template.test.tsx` (layout presence + landmark roles)

## Recommended folder conventions

- `src/templates/<TemplateName>/...`

## Template checklist

- [ ] Defines regions clearly
- [ ] Minimal styling assumptions; flexible composition
- [ ] No domain logic or data acquisition

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/andireuter) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
