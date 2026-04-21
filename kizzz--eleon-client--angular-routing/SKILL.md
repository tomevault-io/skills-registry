---
name: angular-routing
description: Angular 20+ routing specialist for route tables, lazy loading, guards, resolvers, and route-driven state. Use after the `angular` router when navigation structure or route transitions are the main concern. Use when this capability is needed.
metadata:
  author: kizzz
---

# Angular Routing

## When to use

- Adding or refactoring route trees, child routes, redirects, or lazy loading
- Wiring guards, resolvers, route data, titles, or route-level providers
- Passing route params/query params into feature state or forms
- Debugging navigation bugs, unexpected redirects, or route transition regressions

## Do not use

- General component/page rendering with no navigation decision; use [`angular-ui-patterns`](../angular-ui-patterns/SKILL.md)
- Form modeling with minor route context; use [`angular-forms`](../angular-forms/SKILL.md)
- Cross-feature state design as the main topic; use [`angular-state-management`](../angular-state-management/SKILL.md)
- SSR-only route rendering strategy; use [`angular-ssr`](../../skills-optional/angular-ssr/SKILL.md)

## Instructions

1. Keep one clear routing table per feature boundary. Avoid scattering route ownership.
2. Prefer lazy loading for feature areas and heavier standalone screens.
3. Use functional guards and resolvers when the logic is route-scoped.
4. Treat params and query params as inputs, not as hidden globals.
5. Prefer route data for static metadata and titles, not for mutable runtime state.
6. If route-level services are needed, provide them at the route boundary instead of root.
7. Validate transitions after changes: direct navigation, deep links, refresh, back/forward, protected routes.

## Default rules

- Routes own navigation structure
- Stores own long-lived feature state
- Forms own temporary edit state
- Guards block access; resolvers preload what the first paint needs

## Related skills

- [`angular`](../angular/SKILL.md)
- [`angular-state-management`](../angular-state-management/SKILL.md)
- [`angular-forms`](../angular-forms/SKILL.md)
- [`angular-di`](../angular-di/SKILL.md)
- [`frontend-verification`](../frontend-verification/SKILL.md)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kizzz) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
