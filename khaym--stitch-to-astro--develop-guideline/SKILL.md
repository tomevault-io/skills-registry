---
name: develop-guideline
description: Astro coding conventions and rules. Loaded by the developer subagent via skills field. Use when this capability is needed.
metadata:
  author: khaym
---

# Develop Guideline

Coding conventions and rules for implementing Astro sites from Stitch design data.

## File Naming

- Components: `PascalCase.astro` (e.g., `HeroSection.astro`)
- Pages: `kebab-case.astro` or `[...slug].astro` for dynamic routes
- Styles: `kebab-case.css`
- Content: `kebab-case/index.mdx`

## Component Structure

```astro
---
// 1. Imports
import BaseLayout from "../layouts/BaseLayout.astro";

// 2. Props interface
interface Props {
  title: string;
}

// 3. Data fetching / processing
const { title } = Astro.props;
---

<!-- 4. Template -->
<section class="hero">
  <h1>{title}</h1>
  <slot />
</section>

<!-- 5. Scoped styles -->
<style>
  .hero {
    padding: var(--spacing-lg);
    color: var(--color-text);
  }
</style>
```

## CSS Rules

- Use CSS Custom Properties from `global.css` for design tokens
- **Prefer `var(--*)` tokens** for font-size, font-weight, color, spacing, and border-radius to maintain site-wide consistency. If no suitable token exists, add one to `global.css` first
- Component-specific one-off values (e.g., a unique `max-width` or `letter-spacing` for a single component) may be inlined in scoped styles when tokenizing would not benefit reuse
- Write scoped styles in each component (Astro auto-scopes `<style>` tags)
- Use `is:global` only when absolutely necessary (e.g., content collection rendered HTML)
- Responsive: mobile-first with `@media (min-width: 768px)` for desktop
- Prefer `rem` units; use `px` only for borders, shadows, and fine-tuned values
- Use **Astro scoped CSS + CSS Custom Properties** (no Tailwind or similar utility frameworks)

## Content Collections

- Define schemas in `src/content/config.ts` using Zod
- Use `getCollection()` and `getEntry()` for data access
- Dynamic routes via `[...slug].astro` with `getStaticPaths()`

## Image Handling

- Store images in `src/assets/images/` or co-located in `src/content/`
- Use Astro `<Picture>` component for optimization (generates multiple formats like WebP/AVIF and responsive sizes)
- Large images (>2560px): resize before committing

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/khaym) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
