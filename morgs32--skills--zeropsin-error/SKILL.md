---
name: zerospin-error
description: Create and map ZerospinError instances for Effect-based code in zerospin. Use when defining error codes/messages, wrapping causes, mapping/catching errors in Effects, or returning structured errors from Effect.gen/Effect.fn or promise boundaries. Use when this capability is needed.
metadata:
  author: morgs32
---

# ZerospinError

Create structured errors with stable codes, human messages, and optional metadata.

## Quick patterns

- Prefer full form with `code`, `message`, and `cause` when wrapping.
- Use the short form only for trivial cases: `new ZerospinError('some-code')`.
- Return errors from `Effect.gen` with `return yield* new ZerospinError(...)`.

```typescript
const error = new ZerospinError({
 code: 'failed-to-fetch',
 message: 'Failed to fetch data',
 cause: originalError,
 extra: { url },
 status: 502,
});
```

## Rules

- Use a stable, kebab-case `code` string; never include dynamic data in the code.
- Keep `message` human-readable and specific to the failure.
- Always attach the original `cause` when wrapping another error.
- Use `extra` for structured metadata (ids, inputs, context), not for free-form text.
- Use `status` only for HTTP-facing errors.

## Effect usage

### Return it don't throw it, duh

```typescript
const res = yield* Effect.promise(() => fetch('/api/data'));
if (res.status === 404) {
 return yield* new ZerospinError({
  code: 'resource-not-found',
  message: 'Resource not found',
 });
}
```

### Working with promises - mapError!

```typescript
const parsed = Effect.promise(() => res.json()).pipe(
 Effect.mapError((error) => {
  return new ZerospinError({
   code: 'failed-to-parse-json',
   message: 'Failed to parse JSON',
   cause: error,
  });
 })
);
```

### Effect.fn



### Catch all

```typescript
const safe = effect.pipe(
 Effect.catchAll((error) => {
  return new ZerospinError({
   code: 'operation-failed',
   message: error.message,
   cause: error,
  });
 })
);
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/morgs32) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
