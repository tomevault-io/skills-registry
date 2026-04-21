---
name: typescript-best-practices
description: Guidelines for writing clean, type-safe, and maintainable TypeScript code. Use when writing new code or refactoring to ensure strict typing. Use when this capability is needed.
metadata:
  author: nam088
---

# TypeScript Best Practices Skill

## When to use
- When writing any `.ts` files.
- When fixing type errors or refactoring legacy code.
- When the user asks to "fix types" or "improve type safety".

## Guidelines

### 1. No `any`
- **Avoid**: Explicitly using the `any` type (`data: any`).
- **Why**: It defeats the purpose of TypeScript.
- **Fix**: Use `unknown` if the type is truly not known yet, or define a proper Interface/Type. Use type guards (`if (typeof data === 'string')`) to safely narrow `unknown`.

### 2. Type vs Interface
- **Preference**: Use `interface` for object definitions (extensible) and `type` for unions, intersections, or primitives.
  ```typescript
  interface User {
    id: string;
    name: string;
  }
  type UserId = string;
  type UserStatus = 'active' | 'inactive';
  ```

### 3. Utility Types
- Leverage standard utility types to avoid duplication:
  - `Partial<T>`: Make all properties optional.
  - `Pick<T, K>`: Select specific keys.
  - `Omit<T, K>`: Exclude specific keys.
  - `ReturnType<T>`: Get return type of function.

### 4. Strict Mode Compliance
- Ensure code handles `null` and `undefined` explicitly.
- Use optional chaining (`?.`) and nullish coalescing (`??`) instead of loose checks.

### 5. Async/Await
- Always define return types for async functions (`Promise<T>`).
- Avoid `void` for async functions unless it's a fire-and-forget event handler; prefer `Promise<void>`.

### 6. Enums vs Unions
- **Preference**: Prefer **String Literal Unions** over numeric Enums.
- **Why**: Unions are simpler, lighter, and easier to debug.
  ```typescript
  // Good
  type Direction = 'UP' | 'DOWN';
  
  // Avoid
  enum Direction { UP, DOWN }
  ```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/nam088) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
