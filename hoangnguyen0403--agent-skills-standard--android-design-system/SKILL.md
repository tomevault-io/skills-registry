---
name: android-design-system
description: Enforce Material Design 3 and design token usage in Jetpack Compose apps. Use when implementing M3 components, color schemes, or design tokens in Android. (triggers: **/*Screen.kt, **/ui/theme/**, **/compose/**, MaterialTheme, Color, Typography, Modifier, Composable) Use when this capability is needed.
metadata:
  author: hoangnguyen0403
---

# Android Design System (Jetpack Compose)

## **Priority: P2 (OPTIONAL)**

Enforce Material Design 3 tokens in Jetpack Compose. Use `MaterialTheme` for consistency.

## Guidelines

Define `Color.kt`, `Theme.kt`, and `Type.kt` in `ui/theme/`. Map every raw color/type value to `lightColorScheme`/`darkColorScheme` slots. Access all tokens through `MaterialTheme`:
- Colors → `MaterialTheme.colorScheme.*`
- Text styles → `MaterialTheme.typography.*`
- Spacing → `.dp` units consistently

## Anti-Patterns

- **No Hardcoded Colors**: Use `MaterialTheme.colorScheme.*`, not `Color(0xFF...)`.
- **No Inline Typography**: Use `MaterialTheme.typography.*`, not raw `fontSize = 32.sp`.
- **No Magic Spacing**: Prefer named `.dp` tokens; avoid unexplained magic numbers.

## References

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hoangnguyen0403) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
