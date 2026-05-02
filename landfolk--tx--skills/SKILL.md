---
name: tx-styling
description: Guidance for using @landfolk/tx styling (tx prop, grouping syntax, Tailwind transformer, SWC + ESLint plugins) in React/Next.js. Use when this capability is needed.
metadata:
  author: landfolk
---

# tx styling

## When to use
- Editing components that use the `tx` prop or `tx` template literals.
- Integrating `@landfolk/tx` into a React/Next.js app.
- Debugging grouping, Tailwind extraction, or linting around `tx`.

## Core rules
- Prefer `tx` over `className` for Tailwind classes.
- Use arrays for conditional styles; avoid string concatenation.
- Use grouping syntax like `hover:(bg-blue-500 text-white)`.
- Do not forward `tx` props; only forward `className`.
- For styleable components, merge `className` into `tx`.

## Tooling overview
- SWC plugin rewrites `tx` to `className` at compile time.
- Tailwind transformer expands grouped classes for JIT extraction.
- ESLint rule sorts and validates classes.

## Grouping syntax
Example:

```tsx
<div tx="hover:(bg-blue-500 text-white)" />
```

Compiles to `className="hover:bg-blue-500 hover:text-white"`.
Groups can be nested.

## SWC transform
- `tx="..."` becomes `className="..."`.
- Arrays like `tx={[cond && tx`...`]}` become joined `className` arrays.
- Tagged `tx` templates expand at compile time.
- In dev, class strings can be wrapped with a conflict checker if registered.

Register the dev-only conflict checker once:

```ts
import '@landfolk/tx/checkConflicts'
```

## Tailwind transformer
The transformer scans `tx` attributes and template literals to expand grouped
syntax into full class lists so Tailwind JIT sees every class.

## ESLint rule
`@landfolk/tx/optimize-tailwind-classes`:
- Expands grouped syntax and re-compacts in a stable order.
- Sorts classes using Tailwind's internal order.
- Normalizes whitespace in arbitrary values.
- Can validate class names when given a Tailwind config path.

## Getting started
See `getting-started.md` for install, config, and Next.js compatibility.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/landfolk) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
