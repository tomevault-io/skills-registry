---
name: flutter-architecture
description: Defines project structure and architectural conventions for a Flutter app using Riverpod and Supabase. Use when creating new features or organizing code. Use when this capability is needed.
metadata:
  author: code-sinobi
---

# Flutter Architecture Skill

This skill defines how code should be structured in the hoya_app Flutter project.

## When to use this skill

- Creating a new feature or screen
- Adding a new domain model
- Unsure where a file should live
- Refactoring for clarity

## Project structure

Use a feature-first layout:

```
lib/
  features/
    auth/
      data/
      domain/
      presentation/
    home/
    settings/
  core/
    router/
    theme/
    widgets/
    utils/
```

### Layer responsibilities

- **presentation/**
  - Widgets, screens, UI logic
  - Reads state from providers
- **domain/**
  - Plain Dart models and business rules
  - No Flutter imports
- **data/**
  - Supabase queries
  - DTOs and mappers

## Rules

- UI must not talk to Supabase directly
- Providers live close to the feature they serve
- Shared widgets go in `core/widgets`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/code-sinobi) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
