---
name: tailwind-4
description: Tailwind CSS 4.1+ patterns. Use when styling components, responsive design, container queries, masks, shadows, or animations. (project) Use when this capability is needed.
metadata:
  author: tirtza-weinfeld
---

# Tailwind 4.1+

## Setup

```css
@import "tailwindcss";
```

## Required Reading

**Before writing any `@theme` or `@utility`** → READ `utilities.md` and `examples/*.css`

## Avoid → Use

- `tailwind.config.js` → `@theme` in CSS
- `@apply` → `@utility` or raw CSS
- `dark:bg-*` per element → semantic tokens (see `patterns.md`)
- `group` class → `in-*` variant (see `variants.md`)
- `bg-gradient-to-*` → `bg-linear-to-*`

## Patterns

- **Dark mode**: `:root`/`.dark` + `@theme inline` → `patterns.md`
- **Variants**: `in-*`, `has-*`, `nth-*`, negation → `variants.md`
- **Container queries**: `@container`, `@sm:`, `@md:` → `variants.md`
- **Entry animation**: `starting:` variant → `patterns.md`
- **Masks**: `mask-b-from-*`, `mask-radial-*` → `patterns.md`
- **Project utilities**: check `styles/base/utilities.css`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tirtza-weinfeld) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
