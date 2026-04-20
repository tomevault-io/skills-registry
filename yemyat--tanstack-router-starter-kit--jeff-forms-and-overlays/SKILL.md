---
name: jeff-forms-and-overlays
description: Jeff's conventions for TanStack Form and overlay UX (Dialog, Sheet, route-driven state). Use for add/edit flows and modal/sheet interactions. Use when this capability is needed.
metadata:
  author: yemyat
---

# Jeff's Forms and Overlays

Use consistent patterns for maintainable forms and predictable overlay behavior.

## Apply when

- Creating/editing entity forms.
- Adding dialogs, sheets, or confirmations.

## Form rules

- Use `@tanstack/react-form` (`useForm`, `form.Field`, `form.Subscribe`).
- Avoid manual `useState` for submit payload fields.
- Keep field validation near fields or shared schemas.
- Reset form on overlay close where appropriate.

Local `useState` is acceptable for:
- transient search terms
- file objects
- local-only toggles and interaction state

## Overlay rules

- Route-level overlays must use URL query state.
- Opening overlay updates URL; closing clears URL params.
- Derive selected entity from id in URL + loaded data.

## Dialog vs Sheet

Use Dialog for:
- short, blocking, single-purpose actions
- small forms (about 1-4 fields)

Use Sheet for:
- longer/multi-section forms
- workflows needing more context and space

## Sheet defaults

- Use a single scrollable body container.
- Keep action footer visible/sticky.
- Use size by complexity (`sm:max-w-sm` simple, `sm:max-w-lg` denser forms).

## Validation checklist

- [ ] Form submission state uses TanStack Form subscriptions.
- [ ] Overlay open/close is URL-consistent for route-level flows.
- [ ] Dialog/Sheet choice matches form complexity.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/yemyat) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
