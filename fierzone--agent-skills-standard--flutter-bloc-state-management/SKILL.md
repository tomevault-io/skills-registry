---
name: flutter-bloc-state-management
description: Standards for predictable state management using flutter_bloc, freezed, and equatable. Use when this capability is needed.
metadata:
  author: fierzone
---

# BLoC State Management

## **Priority: P0 (CRITICAL)**

## Structure

```text
lib/features/auth/
├── bloc/
│   ├── auth_bloc.dart
│   ├── auth_event.dart # (@freezed or Equatable)
│   └── auth_state.dart # (@freezed or Equatable)
```

## Implementation Guidelines

- **States & Events**: Use `@freezed` for union states. See [references/bloc_templates.md](references/bloc_templates.md).
- **Error Handling**: Use `Failure` objects; avoid throwing exceptions.
- **Async Data**: Use `emit.forEach` for streams.
- **Concurrency**: Use `transformer` for event debouncing.
- **Testing**: Use `blocTest` for state transition verification.
- **Injection**: Register BLoCs as `@injectable` (Factory).

## Anti-Patterns

- **No .then()**: Use `await` or `emit.forEach()` to emit states.
- **No Logic in Builder**: Perform calculations in BLoC, not inside `BlocBuilder`.
- **No BLoC-to-BLoC**: Use streams to coordinate BLoCs, not direct references.

## Related Topics

layer-based-clean-architecture | dependency-injection | error-handling

---
> Source: [fierzone/agent-skills-standard](https://github.com/fierzone/agent-skills-standard) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
