---
name: typescript-patterns
description: TypeScript coding style and patterns - type safety, error handling, code organization Use when this capability is needed.
metadata:
  author: maxbeworks
---

## Type Safety

- Never use `any` - create proper types/enums instead
- Avoid `unknown` too - only use in extreme edge cases where you truly can't know the type
- Leverage existing types with `Pick`, `Omit`, `Partial`, `Required` rather than creating new ones when possible
- Use `const` assertions for literal types: `as const`
- Discriminated unions for state/variant management

## Enums and Constants

- Use enums for related constants, then iterate over them instead of hardcoding values
- Const enums for compile-time optimization
- Object literals with `as const` when you need both type and runtime access

## Error Handling

- Return Result/Either types for expected errors, not exceptions
- Custom error classes with proper typing for unexpected errors
- Validate at boundaries (API, user input) - trust internal types after that
- Early returns for error cases - fail fast, avoid deep nesting

## Function Design

- Pure functions when possible - no side effects
- Early returns over nested conditionals
- Function declarations for utilities, arrow functions for callbacks
- Single responsibility as a guideline - use judgment, don't blindly split if it makes code less readable

## Code Organization

- Types that are reused across multiple places go in `types/` or similar shared location
- Types specific to one file/implementation that won't be reused much go at top of file under imports
- Colocate related implementations
- Explicit imports over barrel exports (index files)
- File size guideline is 200-300 lines, but use judgment - split when readability suffers, not by arbitrary line count

## Async Patterns

- Always handle Promise rejections
- `Promise.all()` for parallel operations, not sequential awaits
- `Promise.allSettled()` when you need all results regardless of failures
- Don't mix callbacks and Promises

## Naming Conventions

- Booleans: `isLoading`, `hasError`, `canSubmit`, `shouldRetry`
- Event handlers: `handleClick`, `handleSubmit` (handler implementations)
- Callbacks: `onSubmit`, `onClick` (callback props)
- Utilities: verb-first (`formatDate`, `parseResponse`, `validateEmail`)
- Types: PascalCase, purposeful suffixes when needed (`UserResponse`, `CreateUserDto`)

## Anti-Patterns to Avoid

- Nested ternaries - use if/else or extract to function
- Object mutation - use spread or immutable updates
- Type assertions (`as`) unless absolutely necessary - fix the types properly
- Premature abstraction - write it twice before extracting
- Deep nesting - max 2-3 levels, then extract
- Magic numbers/strings - use named constants or enums

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/maxbeworks) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
