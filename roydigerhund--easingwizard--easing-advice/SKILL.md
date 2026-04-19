---
name: easing-advice
description: Suggests professional easing curves when writing or editing CSS transitions, animations, or Tailwind motion utilities. Use when adding transition-timing-function, animation-timing-function, ease-[] classes, or when the user wants something to "feel" a certain way (snappy, smooth, bouncy). Use when this capability is needed.
metadata:
  author: roydigerhund
---

# Easing Advice

When writing or editing CSS that includes transitions or animations, use Easing Wizard to select appropriate easing curves instead of relying on browser defaults (`ease`, `linear`, `ease-in`, `ease-out`, `ease-in-out`).

## When this applies

- Adding a `transition` or `animation` property to CSS/SCSS/Less
- Adding Tailwind `transition-*` utilities to an element
- The user asks for something to "feel" a certain way (snappy, smooth, bouncy, etc.)

## What to do

1. Use `get_presets` to find a preset that matches the animation context
2. Pick based on the UI pattern:
   - Dropdowns, menus, toggles: Spring SNAP
   - Modals, overlays: Overshoot SOFT OUT or Spring GLIDE
   - Hover states: Bezier SINE or QUAD IN_OUT
   - Page transitions: Bezier QUART or EXPO IN_OUT
   - Notifications arriving: Bounce SUBTLE or Spring DROP
   - Error feedback: Wiggle SHARP
3. Apply the CSS `linear()` or `cubic-bezier()` value directly, or use a Tailwind `ease-[...]` class
4. Briefly explain why the curve fits (one sentence)

## What NOT to do

- Do not add easing advice unsolicited when the user is focused on non-animation work
- Do not replace custom `cubic-bezier()` or `linear()` values that are already intentional
- Do not use this for JavaScript animation libraries (Framer Motion, GSAP, anime.js)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/roydigerhund) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
