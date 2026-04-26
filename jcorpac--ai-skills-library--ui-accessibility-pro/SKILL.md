---
name: ui-accessibility-pro
description: Mastering semantic HTML and ARIA to build inclusive web experiences for everyone. Use when this capability is needed.
metadata:
  author: jcorpac
---

# Accessibility Pro

Accessibility is not a feature; it is a fundamental requirement of the web. This skill ensures your UI is usable by people with varying abilities.

## Core Standards
- **Semantic HTML**: Use `<main>`, `<nav>`, `<article>`, and `<button>` correctly. Avoid "div-itis".
- **Focus States**: Never disable `:focus` without providing a high-visibility alternative.
- **Color Contrast**: Ensure a minimum contrast ratio of 4.5:1 for text.

## ARIA (Accessible Rich Internet Applications)
- **Roles**: Use `role="alert"`, `role="dialog"`, etc., only when semantic HTML is insufficient.
- **Aria-Labels**: Provide descriptive labels for icon-only buttons.
- **Live Regions**: Use `aria-live="polite"` for dynamic content updates.

## Testing Checklist
- [ ] **Keyboard Only**: Can you navigate the entire app using only Tab and Space/Enter?
- [ ] **Screen Reader**: Does the content read in a logical order?
- [ ] **Zoom**: Does the layout remain functional at 200% zoom?

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jcorpac) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
