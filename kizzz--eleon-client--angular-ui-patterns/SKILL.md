---
name: angular-ui-patterns
description: Angular 20+ UI state specialist for loading, error, empty, table, dialog, and interaction patterns. Use after the `angular` router for component/page work that needs clean runtime states and enterprise-friendly rendering. Use when this capability is needed.
metadata:
  author: kizzz
---

# Angular UI Patterns

## When to use

- Building or refactoring pages, dashboards, tables, dialogs, drawers, or detail views
- Handling loading, empty, error, success, or optimistic states
- Designing async UI flows around API-backed data
- Standardizing how controls, lists, and page states render in Angular apps

## Do not use

- PrimeNG component selection or theming as the main question; use [`angular-primeng-enterprise`](../angular-primeng-enterprise/SKILL.md)
- Routing or guards as the main question; use [`angular-routing`](../angular-routing/SKILL.md)
- State architecture as the main question; use [`angular-state-management`](../angular-state-management/SKILL.md)
- Pure performance cleanup with no UI-shape decision; use [`angular-best-practices`](../angular-best-practices/SKILL.md)

## Instructions

1. Model UI with explicit states: loading, ready, empty, error, submitting, success.
2. Prefer a single render path per view:
   - error first
   - loading only when no usable data exists
   - empty only when the request succeeded with no results
   - data view otherwise
3. Use Angular control flow (`@if`, `@for`, `@switch`, `@defer`) instead of legacy template branching when practical.
4. Keep templates thin. Push branching, labels, and derived state into signals or view-model helpers.
5. Choose enterprise-safe patterns:
   - tables for dense comparison
   - cards for summary views
   - dialog/drawer for focused edits
   - inline validation for forms
6. Design for failure and retry. Every API-backed screen needs a visible retry or recovery path.

## Default patterns

- Skeletons for predictable layouts
- Spinner or button loading for short inline actions
- Empty state with next action, not only a message
- Toast/inline message for result feedback
- Responsive layouts that collapse cleanly before content becomes unreadable

## Related skills

- [`angular`](../angular/SKILL.md)
- [`angular-state-management`](../angular-state-management/SKILL.md)
- [`angular-forms`](../angular-forms/SKILL.md)
- [`angular-primeng-enterprise`](../angular-primeng-enterprise/SKILL.md)
- [`frontend-verification`](../frontend-verification/SKILL.md)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kizzz) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
