---
name: ui-dynamic-interactions
description: Crafting engaging animations and transitions that make an interface feel responsive and alive. Use when this capability is needed.
metadata:
  author: jcorpac
---

# Dynamic Interactions

Interactions should provide feedback and delight without being distracting. This skill covers the "Vibe" of the UI.

## The Interaction Pipeline
1.  **Trigger**: User action (Click, Hover, Scroll).
2.  **Feedback**: Intermediate state (Active button, Loading spinner).
3.  **Completion**: Final transition (Modal open, Content swap).

## CSS vs. JS Animations
- **CSS**: Best for simple state transitions (`transform`, `opacity`). High performance (handled by the compositor).
- **Web Animations API (JS)**: Best for complex, sequenced animations that require logic or randomization.

## Best Practices
- **Staggering**: Animate list items one after another for a more polished feel.
- **Easing**: Avoid `linear` easing. Use `ease-out` for items entering and `ease-in` for items exiting.
- **Respect Preferences**: Always wrap animations in `@media (prefers-reduced-motion: no-preference)`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jcorpac) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
