---
name: typescript-expert
description: Expert TypeScript development with strict typing, generics, advanced patterns, and best practices Use when this capability is needed.
metadata:
  author: ljchg12-hue
---

# TypeScript Expert

## Purpose
Provide expert TypeScript guidance including strict typing, advanced type patterns, generics, and modern JavaScript features.

## Activation Keywords
- typescript, ts, types, typing
- generics, inference, narrowing
- strict mode, type safety
- interfaces, type aliases

## Core Capabilities

### 1. Type System Mastery
- Strict mode configuration
- Union and intersection types
- Literal types
- Template literal types
- Conditional types
- Mapped types

### 2. Generics
- Generic functions
- Generic classes
- Constraints
- Default type parameters
- Variance annotations

### 3. Advanced Patterns
- Type guards
- Discriminated unions
- Branded types
- Module augmentation
- Declaration merging

### 4. Utility Types
- Partial, Required, Pick, Omit
- Record, Extract, Exclude
- ReturnType, Parameters
- Custom utility types

### 5. Best Practices
- Avoid `any` at all costs
- Prefer `unknown` over `any`
- Use const assertions
- Leverage type inference
- Document complex types

## Instructions

When activated:

1. **Configuration Check**
   - Verify tsconfig.json settings
   - Enable strict mode
   - Check target version

2. **Type Design**
   - Design types before implementation
   - Use interfaces for objects
   - Use type aliases for unions

3. **Implementation**
   - Zero `any` tolerance
   - Explicit return types
   - Proper null handling
   - Use type guards

## Code Examples

### Strict Configuration
```json
{
  "compilerOptions": {
    "strict": true,
    "noUncheckedIndexedAccess": true,
    "exactOptionalPropertyTypes": true
  }
}
```

### Advanced Types
```typescript
// Branded types for type safety
type UserId = string & { readonly brand: unique symbol };
type OrderId = string & { readonly brand: unique symbol };

// Type-safe builder pattern
type Builder<T> = {
  [K in keyof T]-?: (value: T[K]) => Builder<T>;
} & { build(): T };

// Conditional type utility
type NonNullableDeep<T> = T extends object
  ? { [K in keyof T]: NonNullableDeep<NonNullable<T[K]>> }
  : NonNullable<T>;
```

## Example Usage

```
User: "Create a type-safe API client"

TypeScript Expert Response:
1. Define API response types
2. Create generic request function
3. Implement type-safe endpoints
4. Add error type handling
5. Use branded types for IDs
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ljchg12-hue) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
