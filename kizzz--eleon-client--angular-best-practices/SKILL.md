---
name: angular-best-practices
description: Angular 20+ performance and maintainability specialist. Use after the `angular` router for rendering cost, bundle size, change detection, async flow cleanup, and performance-minded refactors. Use when this capability is needed.
metadata:
  author: kizzz
---

# Angular Best Practices

## When to use

- Reviewing or refactoring Angular code for runtime performance
- Reducing re-renders, bundle cost, or unnecessary async churn
- Choosing better defaults for component boundaries, data loading, or rendering
- Hardening new feature code with maintainable Angular 20+ patterns

## Do not use

- General routing, forms, or DI questions with no performance angle; use the owning specialist
- PrimeNG component choice as the main topic; use [`angular-primeng-enterprise`](../angular-primeng-enterprise/SKILL.md)
- Runtime browser validation only; use [`frontend-verification`](../frontend-verification/SKILL.md)
- Dependency/package cleanup only; use [`dependency-analysis`](../../skills-optional/dependency-analysis/SKILL.md)

## Instructions

1. Start with the highest-cost risks:
   - unnecessary re-renders
   - oversized route or feature bundles
   - duplicated API calls and waterfalls
   - heavy tables or lists without lazy strategies
2. Prefer Angular 20+ defaults:
   - signals for local derivation
   - lazy feature boundaries
   - explicit loading states
   - narrow public component inputs
3. Keep performance changes understandable. Do not trade basic readability for minor gains.
4. Treat SSR, hydration, and deferred rendering as optional tools, not default complexity.
5. If a change affects observable behavior, pair it with verification or targeted tests.

## Default rules

- Measure the hot path before broad cleanup
- Optimize route boundaries before micro-optimizing component internals
- Avoid hidden subscriptions and duplicated derived state
- Prefer canonical workspace patterns over bespoke optimization tricks

## Related skills

- [`angular`](../angular/SKILL.md)
- [`angular-ui-patterns`](../angular-ui-patterns/SKILL.md)
- [`angular-state-management`](../angular-state-management/SKILL.md)
- [`angular-primeng-enterprise`](../angular-primeng-enterprise/SKILL.md)
- [`frontend-verification`](../frontend-verification/SKILL.md)
- [`angular-testing`](../angular-testing/SKILL.md)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kizzz) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
