---
name: flutter-core
description: Flutter core mechanics skill hub: BuildContext, widget lifecycle, rendering pipeline, keys, layout constraints, and theming foundations. Use when this capability is needed.
metadata:
  author: melsaeed276
---

# Skill: Flutter Core Mechanics

## Purpose
This hub covers how Flutter actually works: `BuildContext`, widget lifecycle, rendering, keys, layout constraints, and theming.
Understanding these fundamentals prevents many "mystery" bugs in UI, performance, and navigation.

## When to use
- You see layout overflows, "unbounded constraints", or unpredictable sizes.
- You are confused about why `context` "doesn't work here".
- Lists, animations, or forms lose state unexpectedly.

## When NOT to use
- If your issue is purely data/state architecture, start at [state/SKILL.md](../state/SKILL.md).
- If your issue is platform APIs (permissions, notifications), start at [platform/SKILL.md](../platform/SKILL.md).

## Core concepts
- **Widget vs Element vs RenderObject**: configuration vs instance vs layout/paint.
- **Constraints**: parents constrain children; children choose size within constraints.
- **Identity**: keys control how elements match widgets across rebuilds.

## Recommended patterns
- Learn the layout rules before adding "fix" widgets.
- Use keys intentionally, mostly `ValueKey`.
- Keep heavy work out of `build`.
- Use `ThemeExtensions` and tokens to avoid scattered styling.

## Minimal example

Routing to the right doc:

```text
- Context timing/scoping -> build_context.md
- Lifecycle/cancellation/dispose -> widget_lifecycle.md
- Layout overflows/unbounded -> layout_constraints.md
- List identity/state loss -> keys.md
- Performance pipeline -> rendering_pipeline.md
- Global theming -> theming_foundations.md
```

## Edge cases
- Nested navigators and overlays can change which context you need.
- Keys can fix state issues but also create new ones if misused.

## Common mistakes
- Calling `showDialog` in `initState` without a post-frame callback.
- Using `Expanded` inside unbounded parents (e.g., `Column` in `SingleChildScrollView`).

## Testing strategy
- Use widget tests to lock in layout behavior and lifecycle interactions.

## Related skills
- [UI: Responsive layouts](../ui/responsive.md)
- [Performance: Rebuilds](../performance/rebuilds.md)
- [Navigation: Testing](../navigation/go_router/testing.md)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/melsaeed276) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
