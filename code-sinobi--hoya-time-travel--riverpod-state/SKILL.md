---
name: riverpod-state
description: Manages application state using Riverpod 3 with code generation. Use when creating providers, async logic, or stateful features.
metadata:
  author: code-sinobi
---

# Riverpod State Management Skill

Follow Riverpod 3 best practices using generated providers.

## When to use this skill

- Adding new state
- Fetching async data
- Managing user/session state

## Conventions

- Prefer `@riverpod` generators
- Name providers by responsibility, not type
- Keep providers small and composable

## Patterns

### Async data
- Use `AsyncValue<T>`
- Handle loading/error in UI, not provider

### Side effects
- Perform mutations inside notifier methods
- Never mutate state directly in widgets

## File naming

- `*_provider.dart`
- `*_notifier.dart`

## Anti-patterns to avoid

- Reading providers inside constructors
- Large monolithic providers
- Business logic in widgets

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/code-sinobi) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
