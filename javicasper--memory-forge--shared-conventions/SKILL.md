---
name: shared-conventions
description: | Use when this capability is needed.
metadata:
  author: javicasper
---

# Shared Conventions

This skill contains project-wide patterns that apply to all apps and modules.

## Conventions

### Commit Message Format

```
type(scope): description

Types: feat, fix, refactor, chore, docs, test
Scope: app name or module (e.g., booking, hermes, shared)
```

### Code Organization

All apps follow this structure:
```
src/
├── domain/         # Business logic, entities, value objects
├── application/    # Use cases, services
└── infrastructure/ # Controllers, repositories, external APIs
```

### Testing

- Unit tests: `*.spec.ts` (alongside source files)
- Integration tests: `*.test.ts` (alongside source files)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/javicasper) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
