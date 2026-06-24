---
name: android-jetpack-compose
description: Standards for Declarative UI, State Hoisting, and Performance Use when this capability is needed.
metadata:
  author: fierzone
---

# Jetpack Compose Standards

## **Priority: P0**

## Implementation Guidelines

### State Hoisting

- **Pattern**: `Screen` (Stateful) -> `Content` (Stateless).
- **Events**: Pass lambda callbacks down (`onItemClick: (Id) -> Unit`).
- **Dependencies**: NEVER pass ViewModel to stateless composables.

### Performance

- **Recomposition**: Use `@Stable` / `@Immutable` on UI Models.
- **Lists**: Always use `key` in `LazyColumn` / `LazyRow`.
- **Modifiers**: Reuse Modifier instances or extract to variables if stable.

### Theming (Material 3)

- **Tokens**: Use `MaterialTheme.colorScheme` and `MaterialTheme.typography`.
- **Hardcoding**: `**No Hardcoded Colors**: Use Theme.`

## Anti-Patterns

- **Side Effects**: `**No SideEffects in Composition**: Use LaunchedEffect.`
- **ViewModel pass-through**: `**No VM deep pass**: Hoist state.`

## References

- [Patterns & Optimization](references/implementation.md)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/fierzone) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
