---
name: mobile-audit
description: Audit and fix mobile responsiveness issues. Use when the user reports mobile UI problems, shares mobile screenshots, or asks to check responsive design. Use when this capability is needed.
metadata:
  author: ntuaisafety
---

# Mobile Responsiveness Audit

Systematically find and fix mobile UI issues in this Hugo/Blowfish site.

## Context

- CSS design system: `assets/css/custom.css` (~1000 lines, Linear-inspired)
- Theme CSS: `themes/blowfish/` (vendored Blowfish v2.98.0, uses Tailwind)
- Breakpoints: Tailwind defaults — `sm:640px`, `md:768px`, `lg:1024px`
- Custom CSS overrides theme CSS — check both when debugging

## Audit Process

1. **Identify the issue** from the user's screenshot or description
2. **Locate the relevant template** in `layouts/` (check which page/partial renders the broken element)
3. **Trace the CSS chain** — grep for the element's classes in BOTH:
   - `assets/css/custom.css` (project overrides)
   - `themes/blowfish/assets/css/` (theme defaults)
4. **Check for mobile-specific problems:**
   - z-index stacking (mobile menus vs content layers)
   - `overflow: hidden` missing on containers
   - Fixed/absolute positioning breaking on small screens
   - Text overflow or wrapping issues
   - Touch targets too small (minimum 44x44px)
   - Horizontal scroll caused by elements wider than viewport
   - Flexbox/grid not wrapping on narrow screens
5. **Write the fix** using mobile-first approach:
   - Prefer Tailwind responsive classes in templates when possible
   - Use `@media` queries in `custom.css` for custom components
   - Never modify files in `themes/blowfish/` — override in project files
6. **Verify** by building with `hugo server -D` and describing expected mobile behavior

## Fix Guidelines

- Keep fixes minimal — target the specific breakpoint causing the issue
- Use existing Tailwind classes before writing custom CSS
- If adding `@media` queries, group them with the component they fix
- Test at 375px (iPhone SE), 390px (iPhone 14), and 768px (iPad) widths
- Check both portrait and landscape orientations for layout issues

## Common Blowfish Mobile Issues

- **Mobile nav z-index:** Blowfish mobile menu may not cover custom hero sections — check `z-index` on both `.header` and hero container
- **Hero text overflow:** Long headlines or typewriter text may overflow on narrow screens
- **Card grids:** `grid-cols-2` without `grid-cols-1` fallback for mobile
- **Absolute positioned decorative elements:** Glow effects, background blurs may cause horizontal scroll
- **Font sizes:** Desktop heading sizes may be too large for mobile

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ntuaisafety) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
