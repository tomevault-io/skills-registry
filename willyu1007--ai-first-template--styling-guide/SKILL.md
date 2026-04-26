---
name: styling-guide
description: Styling patterns (design tokens, theming, and consistent component styling). Keywords: css, styling, design tokens, theming, css modules, tailwind. Use when this capability is needed.
metadata:
  author: willyu1007
---

# Styling Guide

This skill provides styling guidelines that work across common approaches (CSS modules, CSS-in-JS, utility classes, design systems).

---

## 1. Principles

- Prefer a consistent approach across the app.
- Use design tokens (spacing, colors, typography) to avoid "magic numbers".
- Keep component styling colocated unless it becomes large.

---

## 2. Colocation rule of thumb

- Small styles (< ~100 lines): inline or colocated in the component file
- Larger styles: separate `*.styles.ts` / `*.module.css` / similar (depending on stack)

---

## 3. Accessibility considerations

- Ensure sufficient color contrast.
- Preserve focus outlines (or provide accessible alternatives).
- Avoid conveying meaning by color alone.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/willyu1007) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
