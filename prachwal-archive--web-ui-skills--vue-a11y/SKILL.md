---
name: vue-a11y
description: Use when designing, implementing, testing, or reviewing accessibility in Vue 3 apps — semantic HTML, ARIA, keyboard flow, focus management, forms, contrast, motion, responsive behavior, or WCAG 2.2 concerns.
metadata:
  author: prachwal-archive
---

# Vue Accessibility

Use this skill for accessibility work in Vue 3 frontend applications.

## Baseline

- Treat WCAG 2.2 AA as the practical target unless the user specifies another level.
- Start with semantic HTML before ARIA.
- Use ARIA to add missing semantics, not to override native elements that already work.
- Preserve visible focus indicators and logical keyboard order.
- Check both light and dark themes when color changes are involved.

## Semantic Structure

- Each page should have one meaningful `<h1>`.
- Use landmarks intentionally: `<header>`, `<nav>`, `<main>`, `<section>`, `<footer>`, `<form>`.
- Give icon-only buttons an accessible name with `aria-label` or visible text.
- Use real `<button>` for actions and `<a>` for navigation.
- Associate form controls with `<label>`.
- Use `<fieldset>` and `<legend>` for grouped controls.

## Keyboard And Focus

- All interactive controls must be reachable and operable by keyboard.
- Focus order should match visual and reading order.
- Do not remove outlines unless replacing them with an equally visible focus style.
- Manage focus after route changes, dialogs, drawers, and destructive confirmations.
- Escape should close dismissible overlays.
- Avoid keyboard traps; if focus is trapped in a modal, restore focus on close.

## ARIA Rules

- Prefer native controls over custom widgets.
- Do not add `role="button"` to non-button elements unless keyboard handling and focus are fully implemented.
- Keep `aria-expanded`, `aria-controls`, `aria-current`, `aria-invalid`, `aria-describedby` synchronized with visible state.
- Do not hide focusable content with `aria-hidden="true"`.
- Use live regions sparingly for async status changes.

## Vue-Specific Patterns

- Bind accessible state directly from reactive state so DOM and state cannot drift.
- Use `v-bind="$attrs"` on root elements to forward ARIA attributes through wrapper components.
- Use `$nextTick` + `.focus()` after reactive DOM updates that require focus movement.
- Use `<Teleport>` for modals and overlays to keep them in the correct DOM position.
- Use `<Transition>` and `<KeepAlive>` — ensure focus management on enter/leave.
- Declare `aria-live` regions in the root `App.vue` layout, not in conditional components.
- Use `useTemplateRef` (Vue 3.5+) or template refs for DOM access in Composition API.
- Test slots and conditional rendering for missing headings, labels, and status text.

## Review Checklist

- Can the main workflow be completed with keyboard only?
- Is the current page and active navigation understandable to a screen reader?
- Are form errors announced and tied to fields?
- Are loading, empty, error, and success states perceivable?
- Does focus remain visible and predictable after interaction?
- Do both light and dark themes preserve contrast?
- Does the layout work at mobile width and with zoomed text?

## Verification

- Use unit tests for conditional labels, ARIA state, and emitted accessibility behavior.
- Use browser verification for focus order, tab stops, theme contrast, and responsive layout.
- Integrate axe-core in CI or test pipeline for automated checks.

---
> Source: [prachwal-archive/web-ui-skills](https://github.com/prachwal-archive/web-ui-skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-02 -->
