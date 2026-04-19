---
name: project-conventions
description: Code patterns for mta-website. Apply when writing or reviewing code. Use when this capability is needed.
metadata:
  author: itayost
---

## Component Patterns
- Named exports only (no default exports for components)
- Props interface defined above component
- Use `cn()` for all conditional classNames
- Card component: use `hover` prop for interactive cards, `glass` prop for glassmorphism

## Design Tokens
- Colors: oklch values in globals.css @theme block
- Never use raw hex/rgb — always use token classes (primary-*, accent-*, neutral-*)
- Animations: animate-fade-in, animate-fade-in-up, animate-pulse-soft, animate-float

## RTL Rules
- NEVER use left-*/right-*/pl-*/pr-* — use start-*/end-*/ps-*/pe-*
- MobileNav translate: `ltr:translate-x-full rtl:-translate-x-full`
- Use `inset-inline-start-0` not `left-0`

## Accessibility
- Interactive elements need focus-visible styles (global base handles most)
- Forms: wire aria-describedby to error IDs, set aria-invalid on error
- Dialogs: role="dialog" + aria-modal="true" + focus trap + Escape handler
- Nav elements: aria-label in Hebrew
- Live regions: role="status" + aria-live="polite" for success states

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/itayost) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
