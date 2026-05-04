---
name: ts
description: > Use when this capability is needed.
metadata:
  author: todor-mazgalov
---

# TypeScript Language Best Practices

Write clean, strictly-typed, idiomatic modern TypeScript. Prioritize precise
types, inference, and compile-time safety over runtime checks and `any`.

## Before Writing Code

1. **Read existing code first.** Search for existing types, interfaces, utility
   types, and helpers. Reuse before creating.
2. **Check `tsconfig.json`.** Note `target`, `module`, `moduleResolution`,
   `strict`, `lib`, and `jsx` settings. Use language features the target
   supports. If `strict` is off, prefer to turn it on before adding new code.
3. **Check the TypeScript version.** `const` type parameters (5.0+), `satisfies`
   (4.9+), `using` declarations (5.2+), `NoInfer<T>` (5.4+), decorators (5.0+).
   Use the newest features the project supports.

## Compiler Configuration

Always enable in `tsconfig.json`:

- `"strict": true` — turns on all strict flags
- `"noUncheckedIndexedAccess": true` — indexing returns `T | undefined`
- `"noImplicitOverride": true`
- `"exactOptionalPropertyTypes": true` — distinguishes missing vs. `undefined`
- `"noFallthroughCasesInSwitch": true`
- `"forceConsistentCasingInFileNames": true`
- `"isolatedModules": true` — required for bundlers/SWC/esbuild
- `"skipLibCheck": true` — only to speed up, not to hide your own errors

## Type System

### Never use `any`

- `any` disables type checking and infects every expression it touches.
- Prefer `unknown` for values of unknown shape — forces narrowing before use.
- Use generics for functions that accept varied input types.
- If you truly cannot type something, write `// eslint-disable-next-line` with
  a comment explaining why, so the escape hatch is visible.

```ts
// BAD
function parse(input: any) { return JSON.parse(input); }

// GOOD
function parse(input: string): unknown { return JSON.parse(input); }
```

### Prefer `type` aliases and discriminated unions over class hierarchies

```ts
type Result<T, E = Error> =
  | { ok: true; value: T }
  | { ok: false; error: E };

function handle(r: Result<number>) {
  if (r.ok) return r.value * 2; // narrowed
  console.error(r.error);       // narrowed
}
```

### Avoid `enum`; use const objects or literal unions

```ts
// BAD
enum Status { Active, Inactive }

// GOOD
const Status = { Active: "active", Inactive: "inactive" } as const;
type Status = typeof Status[keyof typeof Status];
```

Reasons: `const enum` breaks `isolatedModules`; numeric enums have no runtime
safety; literal unions are erasable and narrow better.

### Use `as const` and `satisfies`

- `as const` makes literals readonly and narrow.
- `satisfies` validates a value matches a type **without widening** it.

```ts
const routes = {
  home: "/",
  profile: "/profile/:id",
} as const satisfies Record<string, `/${string}`>;

type RouteKey = keyof typeof routes; // "home" | "profile"
```

### Leverage built-in utility types

`Partial`, `Required`, `Readonly`, `Pick`, `Omit`, `Record`, `Exclude`,
`Extract`, `NonNullable`, `ReturnType`, `Parameters`, `Awaited`. Reach for
these before writing custom mapped/conditional types.

### Prefer `unknown` over `any` in catch clauses

```ts
try { ... }
catch (e: unknown) {
  if (e instanceof Error) console.error(e.message);
  else console.error(String(e));
}
```

(Requires `"useUnknownInCatchVariables": true`, which `strict` enables.)

### Narrow with type predicates, not casts

```ts
// BAD
const u = value as User;

// GOOD
function isUser(v: unknown): v is User {
  return typeof v === "object" && v !== null && "id" in v && "email" in v;
}
```

## Modules and Imports

- Use ESM syntax: `import`/`export`, not `require`.
- Use explicit `.js` extensions in relative imports when the project is native
  ESM under `"moduleResolution": "NodeNext"` / `"bundler"` with strict modes.
- Prefer named exports over default exports — better refactor support.
- Use `import type` for type-only imports so bundlers can elide them.

```ts
import type { User } from "./user";
import { fetchUser } from "./user";
```

## Asynchrony

- Always `await` promises or return them; never fire-and-forget without intent.
- Prefer `Promise.all` / `Promise.allSettled` for concurrent work.
- Use `AbortController` for cancellation — especially for `fetch`.
- Type async functions with `Promise<T>` explicitly at module boundaries.

## DOM and Browser Types

- Use the built-in DOM lib types: `HTMLElement`, `Event`, `KeyboardEvent`, etc.
- Don't cast from `Element` to `HTMLInputElement` blindly — narrow via
  `instanceof HTMLInputElement`.
- For query results, remember `querySelector` returns `T | null`.

```ts
const input = document.querySelector<HTMLInputElement>("#name");
if (!input) throw new Error("input missing");
input.value = "...";
```

## Errors and Nullability

- Model expected failures as return values (`Result<T, E>`), not thrown errors.
- Reserve `throw` for genuinely exceptional cases.
- Never silently ignore `null`/`undefined` — narrow explicitly or use the
  nullish coalescing (`??`) and optional chaining (`?.`) operators.

## Common Mistakes to Avoid

- **Using `any` "temporarily"** — it spreads. Use `unknown` and narrow.
- **`as` casts without narrowing** — lies to the compiler.
- **Non-null assertions (`!`)** — same problem; use narrowing.
- **`Function` and `Object` types** — too broad; use precise signatures.
- **Enums** — use literal unions instead.
- **`namespace`** — use ES modules.
- **Default exports** — prefer named exports.
- **Mutating readonly arrays/objects** — use spread/immutable updates.
- **Forgetting `void` in callback signatures** — `() => void` lets callers
  return anything, which is usually what you want.

## Code Style

- Match the project's existing formatter (Prettier / Biome / dprint). Do not
  reformat files you aren't already editing.
- Prefer explicit return types on exported functions — improves inference and
  documents the API.
- Keep type definitions close to where they're used unless they're shared.
- One concept per type; compose with intersections and utility types.

## Reuse Before Writing New Code

- Grep for existing types before defining a new one. Duplicate types drift.
- Extract shared types into a `types.ts` (or per-feature type files) only when
  reused — don't pre-abstract.
- Prefer composition (`type A = B & { extra: string }`) over re-declaring.

---
> Source: [todor-mazgalov/forjis-plugins](https://github.com/todor-mazgalov/forjis-plugins) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-04 -->
