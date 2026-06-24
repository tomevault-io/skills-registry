---
name: create-component
description: Add a new component to the design system Use when this capability is needed.
metadata:
  author: eqtylab
---

# Create Component

When creating Equality components,

## Common Patterns

### States

We commonly use the following states for components:

- Neutral (Default)
- Primary (A hierarchical increase, uses the primary brand color)
- Success (Something has worked correctly)
- Warning (The user should be informed that something may not be working as intended or requires attention)
- Danger (Destructive actions or serious errors)

### Prefix and Suffix Slots

Components like "Button" and "Input" have `prefix` and `suffix` slots to place icons or buttons before, or after the primary component content.

## Documentation

After creating the component in the "ui" package, always create a corresponding docs MDX under `packages/demo/src/content/components`.

Review the `create-documentation` skill for instructions on how to format the docs.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/eqtylab) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
