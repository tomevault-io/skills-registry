---
name: using-tailwind-design-tokens
description: > Use when this capability is needed.
metadata:
  author: wredzio
---

# Tailwind Design Tokens

Always use Tailwind design tokens defined in `@/styles/globals.css` and never hardcode design values. If a token doesn't exist, add it to the design system first.

## Core Principle

**NEVER hardcode values. ALWAYS use design tokens.**

1. **Colors**: Always use semantic color tokens (bg-primary, text-foreground, etc.)
2. **Spacing**: Use Tailwind spacing scale (p-4, m-6) or custom tokens (h-19, w-100)
3. **Typography**: Use Typography component or Tailwind text utilities (text-sm, text-base)
4. **Radius**: Use defined radius tokens (rounded-sm, rounded-md, rounded-lg)
5. **New Values**: Add to design system, never use arbitrary values in components
6. **Inline Styles**: Avoid for design values, use Tailwind classes
7. **Exceptions**: Only for one-off micro-adjustments, prefer scale when possible

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/wredzio) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
