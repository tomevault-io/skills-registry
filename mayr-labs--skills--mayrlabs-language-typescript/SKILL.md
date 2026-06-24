---
name: mayrlabs-language-typescript
description: TypeScript is NOT optional. We use it to structurally guarantee application Use when this capability is needed.
metadata:
  author: MayR-Labs
---

# TypeScript Code Doctrine

TypeScript is NOT optional. We use it to structurally guarantee application
integrity at build time, not merely for editor autocompletion.

## The Iron Law

**Never use `any` unless absolutely necessary and heavily justified with a
comment.**

## Core Principles

1. **Strict Everything:** Ensure `strict: true` is in `tsconfig.json`. This
   implies no implicit anys and strict null checks.
2. **Layered Architecture:** Use strict DTOs (Data Transfer Objects) and clear
   interfaces for communication between your Controllers, Services, and
   Repositories.
3. **Interfaces vs Types:** Use `interface` for structural definitions (objects,
   classes) and extendibility. Use `type` for unions, intersections, and
   primitives. Be consistent.
4. **Enums are Banned:** Avoid TS `enum`. They transpile to bloated
   reverse-mapped objects and cause runtime vs build-time confusion. Use const
   objects or union types (`type Status = 'active' | 'inactive'`).

## Best Practices

- **Explicit Return Types:** Always define exact return types for exported
  functions and API boundaries.
- **Type Narrowing:** Use Type Guards (`isUser(obj)`) instead of dangerous type
  assertions (`obj as User`) whenever the source of data is external.
- **Utility Types:** Master and utilize `Omit`, `Pick`, `Partial`, and
  `Readonly` instead of manually duplicating type structures.

## Error Handling

Never throw plain strings. Always throw custom Error classes that extend native
`Error` so that `instanceof` narrowing can be used in your catch blocks.

```typescript
export class ResourceNotFoundError extends Error {
  constructor(message: string) {
    super(message);
    this.name = 'ResourceNotFoundError';
  }
}
```

---
> Source: [MayR-Labs/skills](https://github.com/MayR-Labs/skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-20 -->
