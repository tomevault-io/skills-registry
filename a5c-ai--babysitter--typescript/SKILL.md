---
name: typescript
description: TypeScript configuration, strict mode, generics, and type utilities. Use when this capability is needed.
metadata:
  author: a5c-ai
---

# TypeScript Skill

Expert assistance for TypeScript configuration and patterns.

## Capabilities

- Configure tsconfig
- Implement strict typing
- Create utility types
- Handle generics
- Design type-safe APIs

## Configuration

```json
// tsconfig.json
{
  "compilerOptions": {
    "strict": true,
    "noUncheckedIndexedAccess": true,
    "exactOptionalPropertyTypes": true,
    "noImplicitReturns": true,
    "noFallthroughCasesInSwitch": true,
    "moduleResolution": "bundler",
    "esModuleInterop": true,
    "skipLibCheck": true
  }
}
```

## Utility Types

```typescript
// Extract, Omit, Pick
type UserWithoutPassword = Omit<User, 'password'>;

// Conditional types
type NonNullable<T> = T extends null | undefined ? never : T;

// Mapped types
type Readonly<T> = { readonly [P in keyof T]: T[P] };
```

## Target Processes

- typescript-setup
- type-safety
- api-design

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/a5c-ai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
