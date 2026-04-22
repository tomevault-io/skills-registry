---
name: flutter-state
description: Flutter state management skill hub: async/loading/error models, caching/invalidation, and patterns for Riverpod and BLoC (including testing). Use when this capability is needed.
metadata:
  author: melsaeed276
---

# Skill: State Management (Shared, Riverpod, BLoC)

## Purpose
This hub routes state management topics: how to model state, handle async/loading/error, implement caching/invalidation, and choose patterns for Riverpod or BLoC.

## When to use
- UI has inconsistent loading/error behavior.
- Business rules leak into widgets.
- Multiple features need shared caching or invalidation.
- You are deciding between Riverpod/BLoC or improving an existing setup.

## When NOT to use
- If the issue is pure Dart logic, start with [dart/SKILL.md](../dart/SKILL.md).
- If the issue is networking/storage boundaries, start with [data/SKILL.md](../data/SKILL.md).

## Core concepts
- **Single source of truth**: one owner for state.
- **Immutable state**: predictable transitions.
- **Events vs commands**: choose a model that matches your domain.
- **Async state**: represent loading, data, error, and retry explicitly.

## Recommended patterns
- Keep UI state near UI, domain state near domain.
- Use typed failures and explicit retry paths.
- Cache with explicit invalidation rules; avoid hidden global caches.

## Minimal example

Where to go next:

```text
- Modeling state -> shared/state_modeling.md
- Loading/error/retry -> shared/async_state.md
- Caching/invalidation -> shared/caching_state.md
- Testing state -> shared/testing_state.md
- Riverpod patterns -> riverpod/*
- BLoC patterns -> bloc/*
```

## Edge cases
- Async flows that outlive the screen must be cancelled or ignored.
- Multiple listeners can trigger duplicate loads; coordinate.

## Common mistakes
- Mixing domain logic into widget trees.
- Modeling errors as strings instead of typed failures.

## Testing strategy
- Unit tests for state transitions.
- Widget tests for wiring.
- Integration tests for critical flows.

## Related skills
- [Error modeling](../architecture/error_modeling.md)
- [Repository pattern](../architecture/repository_pattern.md)
- [Testing hub](../testing/SKILL.md)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/melsaeed276) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
