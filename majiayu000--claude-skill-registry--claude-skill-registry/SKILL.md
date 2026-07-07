---
name: next-js-styling-ui-performance
description: Zero-runtime CSS strategies (Tailwind) and RSC compatibility. Use when this capability is needed.
metadata:
  author: majiayu000
---

# Styling & UI Performance

## **Priority: P1 (HIGH)**

Prioritize **Zero-Runtime** CSS for Server Components.

## Library Selection

| Library                    | Verdict            | Reason                                             |
| :------------------------- | :----------------- | :------------------------------------------------- |
| **Tailwind / shadcn**      | **Preferred (P1)** | Zero-runtime, RSC compatible. Best for App Router. |
| **CSS Modules**            | **Recommended**    | Scoped, zero-runtime.                              |
| **MUI / Chakra (Runtime)** | **Avoid**          | Forces `use client` widely. Degrades performance.  |

## Patterns

1. **Dynamic Classes**: Use `clsx` + `tailwind-merge` (`cn` utility).
   - _Reference_: [Dynamic Classes & Button Example](references/implementation.md)
2. **Font Optimization**: Use `next/font` to prevent Cumulative Layout Shift (CLS).
   - _Reference_: [Font Setup](references/implementation.md)
3. **CLS Prevention**: Always specify `width`/`height` on images.

---
> Source: [majiayu000/claude-skill-registry](https://github.com/majiayu000/claude-skill-registry) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-07-07 -->
