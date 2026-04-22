---
name: flutter-architecture
description: Flutter app architecture skill hub: feature structure, clean architecture boundaries, dependency injection, repositories, DTO vs entity, and error modeling. Use when this capability is needed.
metadata:
  author: melsaeed276
---

# Skill: Architecture (Boundaries, Dependencies, Errors)

## Purpose
Architecture keeps complexity manageable: clear boundaries between UI, domain rules, and data/platform details.
This hub routes common architecture topics for Flutter apps.

## When to use
- The codebase is inconsistent across features.
- UI, data, and business logic are tightly coupled.
- You need dependency injection or a repository layer.

## When NOT to use
- Do not over-architect small apps; start simple and add boundaries when needed.

## Core concepts
- **Boundaries**: where responsibilities change.
- **Dependency direction**: higher layers depend on lower-level abstractions.
- **Error contracts**: failures modeled consistently.

## Recommended patterns
- Use feature-first structure.
- Put interfaces in the domain layer.
- Keep DTOs in data layer; map to domain entities.

## Minimal example

Where to go next:

```text
- Folder/module consistency -> feature_structure.md
- Layering and boundaries -> clean_architecture.md
- Dependency injection -> dependency_injection.md
- Repository abstraction -> repository_pattern.md
- DTO vs entity mapping -> dto_vs_entity.md
- Typed failures -> error_modeling.md
```

## Edge cases
- Multi-brand/white-label needs config injection.
- Offline-first needs explicit caching and invalidation.

## Common mistakes
- Leaking API response shapes into UI.
- Circular dependencies between features.

## Testing strategy
- Unit test domain logic.
- Contract test repository behavior with canned data.

## Related skills
- [Data layer](../data/SKILL.md)
- [State modeling](../state/shared/state_modeling.md)
- [Testing hub](../testing/SKILL.md)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/melsaeed276) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
