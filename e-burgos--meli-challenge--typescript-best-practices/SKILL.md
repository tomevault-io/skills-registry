---
name: typescript-best-practices
description: Applies TypeScript best practices for strict mode, typing, utility types, and type safety. Use when writing or reviewing TypeScript code, configuring tsconfig, or enforcing type correctness in frontend or backend. Use when this capability is needed.
metadata:
  author: e-burgos
---

# TypeScript Best Practices

## tsconfig: enable strict

- **`strict: true`** in `tsconfig.json` (or in base config). Enables `noImplicitAny`, `strictNullChecks`, `strictFunctionTypes`, and related checks. Catch errors at compile time instead of runtime.
- **`noImplicitAny`**: No implicit `any`; require explicit types where inference would be `any` (e.g. parameters). Use `unknown` when the type is truly unknown, then narrow.
- **`strictNullChecks`**: `null` and `undefined` are distinct; types must include `| null` or `| undefined` when values can be nullish. Forces handling before property access.
- Optional: `noUnusedLocals`, `noUnusedParameters`, `strictPropertyInitialization`, `exactOptionalPropertyTypes` for stricter code.

## Prefer inference; annotate when needed

- Let TypeScript infer return types and variable types when obvious. Reduces noise and keeps types in sync with implementation.
- Annotate: function parameters (if inference would be `any`), public API boundaries, and when it improves readability or documents intent.
- Avoid redundant annotations (e.g. `const x: number = 1` when inference is clear).

## Avoid any; use unknown and narrow

- **Do not use `any`** as an escape hatch; it disables type checking. Use only when interfacing with untyped JS or when explicitly opting out (and document why).
- **`unknown`**: Use for values whose type is not known. Require type narrowing (typeof, type guards, assertions) before use.
- **Type assertions (`as T`)**: Use sparingly; they bypass checks. Prefer type guards or refactors so the type is inferred. If you assert, ensure the value really is `T`.

## Interfaces and types

- **Interfaces**: For object shapes and contracts; can be extended and merged. Prefer for public API and React props when the project convention is interface.
- **Type aliases**: For unions, intersections, mapped types, and aliasing other types. Use for unions, tuples, and complex compositions.
- Be consistent within the project; both work with utility types.

## Utility types

Use built-in utility types instead of duplicating shapes:

- **`Partial<T>`** — All properties optional (e.g. update payloads).
- **`Required<T>`** — All properties required.
- **`Readonly<T>`** — All properties readonly.
- **`Pick<T, K>`** — Subset of properties (allow-list).
- **`Omit<T, K>`** — Type without listed properties (block-list).
- **`Record<K, V>`** — Object type with keys `K` and value type `V`.
- **`NonNullable<T>`** — Exclude `null` and `undefined`.
- **`ReturnType<F>`** — Return type of function type `F`.
- **`Parameters<F>`** — Tuple of parameter types of `F`.

Prefer `Pick`/`Omit` over copying and pasting properties.

## Null and undefined

- With `strictNullChecks`: handle `null`/`undefined` explicitly (optional chaining `?.`, nullish coalescing `??`, guards, or `!` only when you have proven non-null).
- Prefer `undefined` for “missing” in optional properties; use `| null` when the value can be explicitly null (e.g. APIs).
- Avoid `strictNullChecks: false` in new code; it hides null/undefined bugs.

## Functions

- Type parameters and return type when they are part of the public API or when inference would be `any`.
- Prefer `(x: string) => number` over `Function` or untyped callbacks.
- Use `void` for return type when the function returns nothing meaningful; avoid returning `undefined` explicitly unless required.
- Async: return `Promise<T>`; inference usually gives this from `async` functions.

## Generics

- Use generics for reusable types and functions that work with multiple types; avoid `any` in generic signatures.
- Add constraints (`extends`) when the generic must have certain properties (e.g. `T extends { id: string }`).
- Prefer a single letter or short name for type parameters (`T`, `K`, `V`) unless a longer name clarifies.

## Discriminated unions and narrowing

- Use discriminated unions (common property, e.g. `type: "success" | "error"`) for state or result types; narrow with `switch` or `if` on the discriminant.
- Use type guards (`x is T`) for custom narrowing; keep guards small and reliable.
- Prefer `satisfies` when you need to validate a value’s shape without widening to a looser type.

## React / frontend (when applicable)

- Type component props with an interface or type; avoid inline object types for complex props.
- Use `React.FC` only if the project convention does; otherwise `function Component(props: Props): JSX.Element` or inferred return.
- Type hooks (useState, custom hooks) so initial value and setter are correctly inferred; use generics for useState when the value can be null initially.
- Type event handlers: `React.ChangeEvent<HTMLInputElement>`, etc., instead of `any`.

## Backend / API (when applicable)

- Type request/response bodies and DTOs with interfaces or types; align with OpenAPI/spec if present.
- Type route handlers and middleware (e.g. `Request`, `Response`, extended types for `req.user`).
- Use `unknown` for parsed JSON or external input; validate and narrow (e.g. with Zod or a type guard) before use.

## Checklist

- [ ] `strict: true` (and desired strict options) in tsconfig.
- [ ] No implicit `any`; use `unknown` and narrow when needed.
- [ ] Null/undefined handled explicitly where `strictNullChecks` is on.
- [ ] Utility types used instead of duplicated shapes.
- [ ] Public APIs and boundaries have clear types.
- [ ] No unnecessary type assertions; prefer inference or type guards.

## Reference

- tsconfig strict options and utility types list: [reference.md](reference.md)
- Official docs: [typescriptlang.org/docs](https://www.typescriptlang.org/docs)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/e-burgos) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
