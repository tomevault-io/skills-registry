---
name: angular-expert
description: Angular 17+ gotchas and decision criteria. Covers signals vs observables, standalone patterns, and common pitfalls Claude gets wrong. Use when this capability is needed.
metadata:
  author: nguyenthienthanh
---

> **AI-consumed reference.** Optimized for Claude to read during execution.
> Human-readable explanation: see [docs/architecture/HIERARCHICAL_PLANNING.md](../../../docs/architecture/HIERARCHICAL_PLANNING.md)
> or [docs/getting-started/](../../../docs/getting-started/) depending on topic.


# Angular Expert — Gotchas & Decisions

Use Context7 for full Angular docs.

## Key Decisions

```toon
decisions[4]{choice,use_when}:
  Signals vs Observables,"Signals for sync UI state. Observables for async streams/HTTP. Bridge with toSignal()/toObservable()"
  NgRx vs Signals,"NgRx for complex shared state with effects. Signals for component/simple app state"
  Standalone vs Module,"Always standalone (Angular 17+). Modules only for legacy"
  OnPush vs Default,"Always OnPush. Requires immutable patterns"
```

## Gotchas

- `@for` requires `track` — use `track item.id` not `trackBy` function
- `takeUntilDestroyed(this.destroyRef)` must be called in injection context (constructor or field init)
- `toSignal()` returns `Signal<T | undefined>` — handle the undefined
- `NonNullableFormBuilder` for typed forms — plain `FormBuilder` loses types
- `@defer (on viewport)` needs `@placeholder` block or nothing renders
- Functional guards/interceptors: use `inject()` not constructor DI

---
> Source: [nguyenthienthanh/aura-frog](https://github.com/nguyenthienthanh/aura-frog) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-18 -->
