---
name: jetpack-compose
description: Android's recommended modern toolkit for building native UI Use when this capability is needed.
metadata:
  author: sami
---

# Jetpack Compose Skill

## Best Practices
1.  **State Hoisting**: Move state up to the caller to make composables stateless and reusable.
2.  **Side Effects**: Use `LaunchedEffect` or `DisposableEffect` for side effects. Never run them directly in the function body.
3.  **Stability**: Ensure classes used in params are `@Stable` or `@Immutable` to skip unnecessary recompositions.

## Common Pitfalls
*   **Excessive Recomposition**: Reading state too high up or not using keys in LazyColumns.
*   **Context**: Holding references to Activity Context inside Composables (Leak risk).

## References
*   [Compose Documentation](https://developer.android.com/jetpack/compose)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sami) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
