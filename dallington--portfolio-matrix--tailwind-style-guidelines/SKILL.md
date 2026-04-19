---
name: tailwind-style-guidelines
description: Enforces consistent Tailwind CSS usage, preventing utility abuse and visual inconsistency. Use when this capability is needed.
metadata:
  author: dallington
---

# Tailwind Style Guidelines

This skill ensures **clean, consistent, and maintainable Tailwind CSS usage**.

## When to use this skill

Use this skill when:
- Writing or refactoring UI components
- Tailwind class lists become long or repetitive
- UI consistency matters

Do NOT use this skill when:
- Styling is outside Tailwind
- The task is purely logic-based

## How to use it

1. Group Tailwind classes logically (layout → spacing → typography → color)
2. Detect repeated utility patterns
3. Extract repeated UI into reusable components
4. Prefer semantic spacing and color tokens

## Styling Rules

- No inline styles
- No arbitrary values unless unavoidable
- Avoid massive `className` strings
- Prefer component extraction over class reuse

## Expected Output

- Clean Tailwind class usage
- Visually consistent UI
- Readable and maintainable components

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/dallington) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
