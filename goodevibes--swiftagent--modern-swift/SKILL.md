---
name: modern-swift
description: Use when writing async/await code, enabling strict concurrency, fixing Sendable errors, migrating from completion handlers, managing shared state with actors, or using Task/TaskGroup for concurrency.
metadata:
  author: goodevibes
---

# Modern Swift (6.2+)

Swift 6.2 introduces strict compile-time concurrency checking with async/await, actors, and Sendable constraints that prevent data races at compile time instead of runtime. This is the foundation of safe concurrent Swift.

## Overview

Modern Swift replaces older concurrency patterns (completion handlers, DispatchQueue, locks) with compiler-enforced safety. The core principle: if it compiles with strict concurrency enabled, it cannot have data races.

## Quick Reference

| Need | Use | NOT |
|------|-----|-----|
| Async operation | `async/await` | Completion handlers |
| Main thread work | `@MainActor` | `DispatchQueue.main` |
| Shared mutable state | `actor` | Locks, serial queues |
| Parallel tasks | `TaskGroup` | `DispatchGroup` |
| Thread safety | `Sendable` | `@unchecked` everywhere |

## Core Workflow

When writing async Swift code:
1. Mark async functions with `async`, call with `await`
2. Apply `@MainActor` to view models and UI-updating code
3. Use `actor` instead of locks for shared mutable state
4. Check `Task.isCancelled` or call `Task.checkCancellation()` in loops
5. Enable strict concurrency in Package.swift for compile-time safety

## References

Load these based on what you need:
- **[Concurrency Essentials](references/concurrency-essentials.md)** — Load when writing new async code or converting completion handlers
- **[Swift 6 Concurrency](references/swift6-concurrency.md)** — Load when using @concurrent, nonisolated(unsafe), or actor patterns
- **[Task Groups](references/task-groups.md)** — Load when running multiple async operations in parallel
- **[Task Cancellation](references/task-cancellation.md)** — Load when implementing long-running or cancellable operations
- **[Strict Concurrency](references/strict-concurrency.md)** — Load when enabling Swift 6 strict mode or fixing Sendable errors
- **[Macros](references/macros.md)** — Load when using or understanding Swift macros like @Observable
- **[Modern Attributes](references/modern-attributes.md)** — Load when migrating legacy code or using @preconcurrency, @backDeployed
- **[Migration Patterns](references/migration-patterns.md)** — Load when modernizing delegate patterns or UIKit views

## Common Mistakes

1. **`@unchecked Sendable` as a quick fix** — Using `@unchecked Sendable` to silence compiler errors means you've opted out of safety. If the error persists after `@unchecked`, your code has a potential data race. Fix the underlying issue instead.

2. **Missing `await` at call sites** — Forgetting `await` when calling async functions is a compiler error, but checking `Task.isCancelled` in a loop without calling `Task.checkCancellation()` silently ignores cancellation.

3. **Capturing `self` in async blocks without `weak`** — Holding a strong reference to `self` in a long-running async task prevents deinit. Always use `[weak self]` in closures or use `.task` which auto-manages the lifecycle.

4. **Not checking task cancellation** — Long-running operations should regularly check `Task.isCancelled` or call `Task.checkCancellation()`, otherwise cancellation signals are ignored.

5. **Forgetting `@MainActor` on UI code and test suites** — Main test struct and view models that update `@Published` properties need `@MainActor`. Forgetting it silently allows cross-thread mutations. Apply `@MainActor` to: view models, view structs, main test structs, and any type that touches UI.

6. **Actor re-entrancy surprises** — `await` inside an actor method can release the lock temporarily. Another task may modify actor state. Design actor methods assuming state can change between `await` points.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/goodevibes) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
