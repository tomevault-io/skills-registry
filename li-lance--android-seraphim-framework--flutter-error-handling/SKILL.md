---
name: flutter-error-handling
description: Implement functional error recovery with Either/Failure patterns. Use when writing repositories, handling exceptions, defining failures, or using Either in any Flutter layer. (triggers: lib/domain/**, lib/infrastructure/**, Either, fold, Left, Right, Failure, dartz) Use when this capability is needed.
metadata:
  author: li-lance
---

# Error Handling

## **Priority: P1 (HIGH)**

Standardized functional error handling using `dartz` and `freezed` failures.

## Implementation Workflow

1. **Define failures** — Create domain-specific failures using `@freezed` unions (e.g.,
   `UnauthorizedFailure`, `OutOfStockFailure`).
2. **Return Either** — Repositories return `Either<Failure, T>`. No exceptions in UI/BLoC.
3. **Catch in Infrastructure only** — Infrastructure catches exceptions (e.g., `DioException`) and
   returns `Left(Failure)`. Never rethrow to UI.
4. **Fold in BLoC** — Use `.fold(failure, success)` in BLoC to emit corresponding states. Remove
   try/catch from BLoC.
5. **Localize messages** — Use `failure.failureMessage` (returns `TRObject` or localized string) for
   UI-safe text.
6. **Log with stable templates** — Use low-cardinality message templates; pass variable data via
   metadata/context.
7. **No Silent Catch**: Never swallow errors without logging or a documented retry.
8. **Crashlytics Routing**: All UI/BLoC `catch` blocks MUST route errors via
   `AppLogger.error(AppException.fromException(e).message, error: e, stackTrace: st)` for
   observability and type-safe UI messages.

### Repository & BLoC Examples

See [implementation examples](references/implementation.md) for repository error mapping and BLoC
consumption patterns.

## Reference & Examples

For Failure definitions and API error mapping:
See [references/REFERENCE.md](references/REFERENCE.md).

## Anti-Patterns

- ❌ `try { … } catch (e) { emit(ErrorState()); }` in BLoC — try/catch belongs only in
  Infrastructure; BLoC receives `Either`, then folds
- ❌ `Left(Failure('Something went wrong'))` using a plain `String` — define typed `@freezed` Failure
  subclasses for each domain error
- ❌ `catch (e) {}` empty catch — always log and propagate; never swallow silently
- ❌ Throwing `Exception` from a repository — return `Left(Failure)` instead; exceptions must not
  cross the infrastructure boundary
- ❌ `catch (e) { print(e); }` — missing `AppLogger.error`; errors must be sent to Crashlytics with
  the original error and stack trace

## Related Topics

layer-based-clean-architecture | bloc-state-management

---
> Source: [li-lance/android-seraphim-framework](https://github.com/li-lance/android-seraphim-framework) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
