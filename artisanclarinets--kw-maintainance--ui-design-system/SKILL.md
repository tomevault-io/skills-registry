---
name: ui-design-system
description: UI guidelines following the "Engineered Hardware" aesthetic. Use when this capability is needed.
metadata:
  author: artisanclarinets
---

# UI Design System

This skill ensures the UI adheres to the "Engineered Hardware" design philosophy.

## Design Philosophy
The interface should feel like a piece of high-precision military or industrial hardware.
*   **Aesthetic:** "Engineered Hardware".
*   **Font:** **JetBrains Mono** (Monospace) for data and headers.
*   **Visuals:** Precise 1px borders, persistent grids, raw technical feel.

## Component Usage

### shadcn/ui
*   **Location:** `components/ui/`
*   **Directive:** Use the provided components. Do not build custom primitives unless necessary.

### Framer Motion
*   **Requirement:** Use `framer-motion` for animations.
*   **Style:** "Subtle and purposeful".
    *   Avoid bouncy or cartoony springs.
    *   Use precise, mechanical easings.

## Accessibility
*   **Standard:** WCAG 2.2 AA.
*   **Focus:** Ensure high contrast and visible focus states (often styled as "laser" or "terminal" cursors in this design system).

## Layout
*   **Grid:** Use persistent background grids to reinforce the technical aesthetic.
*   **Spacing:** Strict adherence to the spacing scale.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/artisanclarinets) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
