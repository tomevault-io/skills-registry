---
name: react-native-dls
description: Enforce design token usage in React Native. Use when enforcing a design system, preventing hardcoded styles, or implementing theme tokens in React Native. (triggers: **/*Screen.tsx, **/*Component.tsx, **/theme/**, **/styles/**, StyleSheet, styled-components, theme, colors, spacing) Use when this capability is needed.
metadata:
  author: hoangnguyen0403
---

# React Native Design System

## **Priority: P1 (OPERATIONAL)**

Enforce design token usage in React Native apps.

## Guidelines

- **Structure**: Define tokens in `theme/colors.ts`, `spacing.ts`, `typography.ts`.
- **Usage**: Import tokens (`colors.primary`) instead of literals (`#000`).
- **Styling**: Compatible with `StyleSheet` and `styled-components`.

## Anti-Patterns

- **No Inline Colors**: Use `'#FF0000'` → Error. Import from `theme/colors`.
- **No Magic Spacing**: Use `padding: 16` → Error. Use `spacing.md`.
- **No Inline Fonts**: Define `fontSize: 20` → Error. Use `typography.h1`.

## References

See [references/usage.md](references/usage.md) for design token usage examples.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hoangnguyen0403) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
