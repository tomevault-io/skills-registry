---
name: flutter-dart
description: Dart language skill hub for Flutter developers: async/await, null safety, collections/immutability, extensions, isolates, and error handling patterns. Use when this capability is needed.
metadata:
  author: melsaeed276
---

# Skill: Dart for Flutter

## Purpose
This hub routes Dart-language topics that strongly impact Flutter apps: async correctness, null safety, immutability, extensions, isolates, and error modeling.

## When to use
- You see async lifecycle bugs ("setState after dispose", stale responses, retries).
- You need safer APIs (null safety, typed errors) or predictable data structures.
- You want to move CPU work off the UI thread.

## When NOT to use
- Your issue is a layout or rendering problem; start at [flutter_core/SKILL.md](../flutter_core/SKILL.md).
- Your issue is state management architecture; start at [state/SKILL.md](../state/SKILL.md).

## Core concepts
- **Event loop**: Futures and microtasks determine ordering.
- **Null safety**: types are contracts; prefer modeling over `!`.
- **Immutability**: predictable state transitions and cheaper tests.
- **Isolates**: concurrency by message passing, not shared memory.

## Recommended patterns
- Model failures as data (sealed failures) where UX/testing benefits.
- Prefer immutable models and copy-on-write updates.
- Cancel or ignore stale async work when the owner is disposed.
- Offload CPU-heavy work (JSON decode, sorting large lists) to an isolate.

## Minimal example

How to pick a topic:

```text
- Async ordering/cancellation -> async.md
- Null crashes / optional fields -> null_safety.md
- Defensive list/map updates -> collections.md
- Reusable helpers -> extensions.md
- CPU work off UI thread -> isolates.md
- Typed failures -> error_handling.md
```

## Edge cases
- Async work may complete after navigation; you must check ownership.
- JSON fields are often missing/nullable; model them explicitly.

## Common mistakes
- Using `dynamic` to "get it working" and losing type safety.
- Treating all failures as exceptions instead of modeling expected errors.

## Testing strategy
- Unit test pure Dart logic and error modeling.
- Use fake clocks/clients to make async tests deterministic.

## Related skills
- [Flutter lifecycle](../flutter_core/widget_lifecycle.md)
- [Async state modeling](../state/shared/async_state.md)
- [Error modeling](../architecture/error_modeling.md)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/melsaeed276) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
