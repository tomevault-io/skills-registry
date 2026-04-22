---
name: flutter-navigation
description: Flutter navigation skill hub: go_router (declarative routing), deep links, guards, nested routes, parameter passing, and Navigator 1.0 legacy patterns. Use when this capability is needed.
metadata:
  author: melsaeed276
---

# Skill: Navigation (go_router + Navigator 1.0)

## Purpose
Navigation is more than pushing routes: deep links, guards, nested routing, and testability all influence architecture.
This hub routes navigation topics for both modern declarative routing (`go_router`) and legacy Navigator 1.0 patterns.

## When to use
- You are adding routes, auth gating, or deep linking.
- You are migrating from imperative navigation to declarative routing.
- You want reliable navigation tests.

## When NOT to use
- Do not introduce a routing library for a tiny prototype unless you need deep links.
- Do not mix multiple navigation paradigms without clear boundaries.

## Core concepts
- **Route state**: the current location + parameters.
- **Guards/redirects**: route-level access control.
- **Nested navigation**: tabs and shell routes.

## Recommended patterns
- Prefer `go_router` for apps that need deep links and web URLs.
- Keep navigation decisions out of widgets; use a central router.
- Use typed parameter parsing and avoid stringly-typed extras.

## Minimal example

Where to go next:

```text
- Start / mental model -> go_router/overview.md
- Setup -> go_router/setup.md
- Parameters -> go_router/parameters.md
- Guards/redirects -> go_router/guards.md
- Deep links -> go_router/deep_links.md
- Nested routes/tabs -> go_router/nested_routes.md
- Testing -> go_router/testing.md
- Legacy stack nav -> navigator_1/*
```

## Edge cases
- Nested navigators can break back behavior if misconfigured.
- Auth refresh + redirects can create loops; test it.

## Common mistakes
- Using the wrong `BuildContext` for `Navigator.of`.
- Putting routing logic inside widgets' `build` methods.

## Testing strategy
- Widget tests for routing config.
- Integration tests for deep links and guard behavior.

## Related skills
- [BuildContext](../flutter_core/build_context.md)
- [Async state](../state/shared/async_state.md)
- [Integration tests](../testing/integration_tests.md)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/melsaeed276) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
