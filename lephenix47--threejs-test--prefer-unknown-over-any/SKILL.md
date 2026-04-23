---
name: prefer-unknown-over-any
description: Use unknown instead of any for type-safe handling of values with unknown types in TypeScript. Use when this capability is needed.
metadata:
  author: lephenix47
---

# Prefer `unknown` Over `any`

## Rule
Use `unknown` to force type checking before usage. Never use `any`.

## ✅ Good (unknown)
```typescript
function processData(data: unknown) {
  if (typeof data === "string") {
    return data.toUpperCase(); // OK - type narrowed
  }
  throw new Error("Expected string");
}
```

## ❌ Bad (any)
```typescript
function processData(data: any) {
  return data.toUpperCase(); // No error, but might crash at runtime
}
```

## Why unknown?
- Forces type guards before use
- Prevents runtime errors
- Maintains type safety
- Makes invalid states unrepresentable

## Common Type Guards
```typescript
// typeof check
if (typeof value === "string") { /* ... */ }

// instanceof check
if (value instanceof Error) { /* ... */ }

// in operator
if (typeof value === "object" && value !== null && "prop" in value) { /* ... */ }

// Custom type guard
function isUser(value: unknown): value is User {
  return typeof value === "object" && value !== null && "name" in value;
}
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/lephenix47) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
