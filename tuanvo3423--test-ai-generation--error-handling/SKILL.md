---
name: flutter-error-handling
description: Typed error handling without exceptions crossing into UI/BLoC. Invoke when designing Failures, repository return types, or mapping Dio errors. Use when this capability is needed.
metadata:
  author: tuanvo3423
---

# Error Handling

## **Priority: P1 (HIGH)**

Standardized typed error handling using `Result<T>` and `Failure` models (no `dartz`, no `freezed` required).

## Implementation Guidelines

- **Result Pattern**: Return `Result<T>` from repositories. No exceptions in UI/BLoC.
- **Failures**: Define domain-specific failures as plain immutable classes (use `Equatable` when needed).
- **Mapping**: Infrastructure catches `Exception` and returns `FailureResult(Failure)`.
- **Consumption**: In BLoC, branch on success/failure to emit corresponding states.

## Reference & Examples

For Failure definitions and API error mapping:
See [references/REFERENCE.md](references/REFERENCE.md).

## Related Topics

layer-based-clean-architecture | bloc-state-management

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tuanvo3423) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
