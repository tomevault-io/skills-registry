---
name: typescript-type-factory
description: Creates and centralizes TypeScript types (Paper, User, Endorsement) under `src/types` to keep store and UI consistent.
metadata:
  author: cargdev
---

# TypeScript Type-Factory

When to use this skill

- Use when adding new domain models or when types diverge between UI and store.
- Triggered by requests to generate types, add optional fields, or migrate type shapes.

Instructions

1. First Step: Create `src/types/index.ts` and define exported interfaces/types: `Paper`, `User`, `Endorsement`, `ApiResponse`.

2. Second Step: Ensure stores, components, and services import types from `@/types` to avoid duplication.

3. Third Step: Add comments and sample fixtures that match `mockData` for easy testing.

Examples

```ts
export type Paper = { id: string; title: string; authors: string[]; categories: string[]; abstract?: string }
```

Notes

- Keep versioning/migrations in mind if types are persisted to localStorage; consider a simple `schemaVersion` field.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/cargdev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
