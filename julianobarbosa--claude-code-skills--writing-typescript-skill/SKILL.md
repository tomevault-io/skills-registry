---
name: writing-typescript
description: Idiomatic TypeScript development. Use when writing TypeScript code, Node.js services, React apps, or discussing TS patterns. Emphasizes strict typing, composition, and modern tooling (bun/vite). Use when this capability is needed.
metadata:
  author: julianobarbosa
---

# TypeScript Development (2025)

## Core Principles

- **Strict typing**: Enable all strict checks
- **Parse, don't validate**: Transform untrusted data at boundaries
- **Composition over inheritance**: Small, focused functions
- **Explicit over implicit**: No `any`, prefer `unknown`

## Toolchain

```bash
bun          # Runtime + package manager (fast)
vite         # Frontend bundling
vitest       # Testing
eslint       # Linting
prettier     # Formatting
```

## Quick Patterns

### Type Guards

```typescript
function isUser(value: unknown): value is User {
  return typeof value === "object" && value !== null && "id" in value;
}
```

### Discriminated Unions

```typescript
type Result<T, E = Error> = { ok: true; value: T } | { ok: false; error: E };

function processResult<T>(result: Result<T>): T {
  if (result.ok) return result.value;
  throw result.error;
}
```

### Utility Types

```typescript
type UserUpdate = Partial<User>;
type UserSummary = Pick<User, "id" | "name">;
type UserWithoutPassword = Omit<User, "password">;
type ReadonlyUser = Readonly<User>;
```

## tsconfig.json Essentials

```json
{
  "compilerOptions": {
    "target": "ES2022",
    "module": "ESNext",
    "moduleResolution": "bundler",
    "strict": true,
    "noUncheckedIndexedAccess": true,
    "exactOptionalPropertyTypes": true,
    "noImplicitReturns": true,
    "isolatedModules": true
  }
}
```

## References

- [PATTERNS.md](PATTERNS.md) - Code patterns and style
- [REACT.md](REACT.md) - React component patterns
- [TESTING.md](TESTING.md) - Testing with vitest

## Commands

```bash
bun install              # Install deps
bun run build            # Build
bun test                 # Test
bun run lint             # Lint
bun run format           # Format
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/julianobarbosa) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
