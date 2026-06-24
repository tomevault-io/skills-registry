---
name: materialui
description: Material UI React component library. Use for React UI. Use when this capability is needed.
metadata:
  author: g1joshi
---

# Material UI (MUI)

MUI Core (v6) is the gold standard for React UI. v6 introduces **Pigment CSS** (Zero-runtime CSS-in-JS) for compatibility with React Server Components (Next.js App Router).

## When to Use

- **Enterprise**: Consistent, rigorous design system.
- **Complex Data**: `MUI X` (DataGrid) is the most powerful table library for React.
- **Accessibility**: First-class a11y support.

## Core Concepts

### The `sx` prop

Superset of CSS. `sx={{ mt: 2, color: 'primary.main' }}`.

### Theme Provider

Global design tokens (`palette`, `typography`, `breakpoints`).

### Slots / SlotProps

Deep customization of internal sub-components.

## Best Practices (2025)

**Do**:

- **Use Pigment CSS**: For RSC compatibility.
- **Use `Box`**: Instead of `div` for layout.
- **Use `MUI X`**: For Data Grids and Date Pickers.

**Don't**:

- **Don't use `makeStyles`**: It is deprecated. Use `styled()` or `sx`.

## References

- [Material UI Documentation](https://mui.com/)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/g1joshi) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
