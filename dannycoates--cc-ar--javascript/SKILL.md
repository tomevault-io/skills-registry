---
name: javascript
description: >- Use when this capability is needed.
metadata:
  author: dannycoates
---

# JavaScript

Best practices for writing modern, type-safe JavaScript using JSDoc annotations checked by TypeScript. Covers type modeling, modern language features (ES2024/2025), web APIs, error handling, and module patterns.

This skill targets plain `.js` files with `checkJs: true` in `jsconfig.json`. It does not cover TypeScript `.ts` files.

## Project Setup

### jsconfig.json

Every JS project using typed JSDoc should have a `jsconfig.json`:

```json
{
  "compilerOptions": {
    "checkJs": true,
    "strict": true,
    "noImplicitAny": true,
    "strictNullChecks": true,
    "target": "ES2024",
    "module": "nodenext",
    "moduleResolution": "nodenext",
    "noEmit": true
  },
  "include": ["src/**/*.js"],
  "exclude": ["node_modules"]
}
```

Adjust `target`, `module`, and `moduleResolution` to match the project's runtime environment. Run type checking with `tsc --project jsconfig.json` or integrate into CI. TypeScript 5.5+ is recommended for full JSDoc support including `@import`.

## Type Modeling with JSDoc

### Core Annotations

Use these JSDoc tags for type safety. TypeScript's checker understands them natively.

| Tag | Purpose | Example |
|-----|---------|---------|
| `@type` | Inline type annotation | `/** @type {string} */` |
| `@param` / `@returns` | Function signatures | `@param {number} x` |
| `@typedef` + `@property` | Named object types | See below |
| `@template` | Generic type parameters | `@template T` |
| `@callback` | Function type definitions | Named function signatures |
| `@import` | Clean type imports (TS 5.5+) | `/** @import { Foo } from './types.js' */` |
| `@satisfies` | Validate type, preserve inference (TS 5.0+) | `/** @satisfies {Config} */` |
| `@overload` | Multiple function signatures (TS 5.0+) | See `references/type-patterns.md` |

### Defining Types

```js
/**
 * @typedef {Object} ElevatorState
 * @property {number} index
 * @property {number | null} destinationFloor
 * @property {'idle' | 'moving' | 'stopped'} status
 */
```

### Importing Types

Prefer the `@import` tag (TS 5.5+) over inline `import()` expressions:

```js
/** @import { ElevatorState, FloorConfig } from './types.js' */
```

Older pattern (still works, more verbose):
```js
/** @type {import('./types.js').ElevatorState} */
```

### Discriminated Unions

Model variant types using a shared discriminant property:

```js
/**
 * @typedef {{ kind: 'success', value: unknown }} Success
 * @typedef {{ kind: 'error', error: Error }} Failure
 * @typedef {Success | Failure} Result
 */

/** @param {Result} result */
function handle(result) {
  if (result.kind === 'success') {
    // Narrowed to Success
    console.log(result.value);
  }
}
```

TypeScript's control flow analysis narrows union types in JS files through `typeof`, `instanceof`, `in`, equality checks, and truthiness checks — no TypeScript syntax required.

### Generic Types

```js
/**
 * @template T
 * @param {Promise<T>} promise
 * @returns {Promise<{ data: T, error: null } | { data: null, error: Error }>}
 */
async function tryCatch(promise) {
  try {
    return { data: await promise, error: null };
  } catch (e) {
    return { data: null, error: /** @type {Error} */ (e) };
  }
}
```

Use `@template {Constraint} T` for bounded generics:

```js
/**
 * @template {string} K
 * @template V
 * @param {Record<K, V>} obj
 * @param {K} key
 * @returns {V}
 */
function getProperty(obj, key) {
  return obj[key];
}
```

### Error Suppression

Prefer `// @ts-expect-error` over `// @ts-ignore`. The former errors when the underlying issue is fixed, preventing stale suppressions.

### Forward Compatibility

TypeScript 7 ("Corsa"), as announced, will drop `@enum` and `@constructor` tags. Use ES classes and string literal unions instead. Avoid relying on these patterns in new code.

For detailed patterns including branded types, overloads, and complex generics, consult `references/type-patterns.md`.

## Modern Language Features

### ES2024 (Stable)

| Feature | Use Case |
|---------|----------|
| `Promise.withResolvers()` | Extract resolve/reject from promise scope |
| `Object.groupBy()` / `Map.groupBy()` | Group array items by computed key |
| `ArrayBuffer.transfer()` | Transfer buffer ownership without copying |
| `String.isWellFormed()` / `toWellFormed()` | Validate/fix Unicode strings |
| RegExp `/v` flag | Set notation in character classes |

### ES2025 (Stable)

| Feature | Use Case |
|---------|----------|
| Set methods | `union()`, `intersection()`, `difference()`, `symmetricDifference()`, `isSubsetOf()`, `isSupersetOf()`, `isDisjointFrom()` |
| Iterator helpers | `.map()`, `.filter()`, `.take()`, `.drop()`, `.flatMap()`, `.reduce()`, `.toArray()` on iterators |
| `Promise.try()` | Wrap sync-or-async function in a promise |
| Import attributes | `import data from './config.json' with { type: 'json' }` |
| `RegExp.escape()` | Escape special regex characters |

### Emerging (Stage 3-4, Check Engine Support)

| Feature | Status | Notes |
|---------|--------|-------|
| Temporal API | Stage 3 | Chrome 144+, Firefox 139+. No Safari. Use polyfill if needed. |
| `using` / Explicit Resource Management | Stage 3 | Chrome 134+. Not yet cross-browser. |
| `Error.isError()` | Stage 4, ES2026 | Cross-realm Error detection |
| `import defer` | Stage 4, ES2026 | Lazy module evaluation |

Prefer stable features. For Stage 3 features, check target runtime support before adopting.

## Web APIs

### AbortController Patterns

Use AbortController for cancellation and cleanup across async operations, event listeners, and resource management:

```js
const controller = new AbortController();

// Combine user cancellation with timeout
const signal = AbortSignal.any([
  controller.signal,
  AbortSignal.timeout(5000)
]);

try {
  const response = await fetch(url, { signal });
} catch (err) {
  if (err.name === 'TimeoutError') { /* timed out */ }
  else if (err.name === 'AbortError') { /* user cancelled */ }
  else throw err;
}
```

Use AbortSignal with `addEventListener` for automatic listener cleanup:

```js
element.addEventListener('click', handler, { signal: controller.signal });
// Later: controller.abort() removes the listener
```

### Other Stable APIs

| API | Use Case |
|-----|----------|
| `structuredClone(obj)` | Deep clone objects (handles Date, Map, Set, ArrayBuffer) |
| `CustomEvent` / `EventTarget` | Event-driven architecture without frameworks |
| `crypto.randomUUID()` | Generate UUIDs |
| `navigator.sendBeacon()` | Reliable analytics/telemetry on page unload |
| `URL` / `URLSearchParams` | URL manipulation without string hacking |
| `TextEncoder` / `TextDecoder` | String-to-bytes conversion |
| `ReadableStream` / `WritableStream` | Streaming data processing |
| `BroadcastChannel` | Cross-tab communication |

## Error Handling

### Error Cause Chains

Wrap errors with context using the `cause` option:

```js
try {
  const data = JSON.parse(raw);
} catch (err) {
  throw new Error('Failed to parse config', { cause: err });
}
```

### AggregateError

Group multiple errors from parallel operations:

```js
const errors = [];
for (const task of tasks) {
  try { await task(); } catch (e) { errors.push(e); }
}
if (errors.length) {
  throw new AggregateError(errors, 'Multiple tasks failed');
}
```

### Pattern Summary

| Pattern | When |
|---------|------|
| `Error` + `cause` | Wrapping a single error with added context |
| `AggregateError` | Grouping multiple independent errors |
| `AbortError` / `TimeoutError` | Cancellation and timeout signaling |

## Module Patterns

### ESM Conventions

- Use `"type": "module"` in `package.json`
- Prefer named exports over default exports for discoverability
- Use the `"exports"` field to define the package's public API
- Avoid barrel files (`index.js` re-exporting everything) — they degrade tree-shaking and increase load time
- Prefer direct imports: `import { thing } from './utils/thing.js'`

### Dynamic Imports

Use `import()` for code splitting and conditional loading:

```js
const { handler } = await import(`./handlers/${type}.js`);
```

### Class Patterns

Use private fields (`#field`) for encapsulation:

```js
class Elevator {
  /** @type {number} */
  #currentFloor;

  /** @type {number | null} */
  #destination = null;

  /**
   * @param {number} startFloor
   */
  constructor(startFloor) {
    this.#currentFloor = startFloor;
  }

  get currentFloor() {
    return this.#currentFloor;
  }
}
```

Separate internal state from public API surfaces using `toJSON()` for serialization and purpose-built API methods for external consumers.

## Additional Resources

- **`references/type-patterns.md`** — Advanced JSDoc type patterns: branded types, overloads, complex generics, conditional patterns, and TS 7 migration guidance.
- **`references/api-reference.md`** — Quick reference for modern Web APIs and ES2024/2025 features with usage examples and browser/runtime support status.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/dannycoates) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
