---
name: tailwind-v4-architect
description: Master guide for Tailwind CSS v4. Enforces CSS-first configuration, native container queries, and Vite-native performance patterns. Use when this capability is needed.
metadata:
  author: rahlplx
---

# Tailwind v4 Architect

> **⚠️ CORE DIRECTIVE**: Treat CSS as the configuration source. Tailwind v4 puts CSS variables and the `@theme` directive at the center of the architecture.

## 1. CSS-Native Configuration

**DO NOT** use `tailwind.config.js` unless absolutely necessary for complex JavaScript plugins.
**DO** use strict CSS variables for theming.

### Required Setup (`src/styles/global.css`)

```css
@import "tailwindcss";

@theme {
  /* Semantic Tokens (oklch preferred for wide gamut) */
  --color-primary: oklch(0.55 0.25 260); /* Blue-ish */
  --color-accent: oklch(0.85 0.15 85);   /* Gold-ish */
  
  /* Fluid Spacing */
  --spacing-4xl: 2.25rem;
  
  /* Fonts */
  --font-display: "Playfair Display", serif;
}
```

## 2. Vite-Native Integration

Tailwind 4 is a PostCSS plugin no more—it's a native Vite plugin.
**Verified Config** (`astro.config.mjs`):

```javascript
import tailwindcss from '@tailwindcss/vite';
export default defineConfig({
  vite: { plugins: [tailwindcss()] }
});
```

## 3. Container Queries (Standard)

Stop using media queries for components. Use container queries to make components portable.

- **Parent**: `@container`
- **Child**: `@md:flex-row` (Apply styles based on parent width)

## 4. Modern Anti-Patterns (To Avoid)

- ❌ `@apply` for simple utilities (creates bloat)
- ❌ `tailwind.config.js` for simple colors
- ❌ `@import` from npm packages (Tailwind 4 verifies these automatically)

## 5. Verification

- Use `npx tailwindcss --help` to verify CLI availability.
- Check browser devtools: CSS variables should appear on `:root`.
- HMR should be <50ms.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rahlplx) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
