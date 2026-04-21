---
name: voltaire-effect-error-handling
description: This skill should be used when the user asks about "voltaire-effect errors", "typed errors Effect", "catchTag", "ParseError", "ProviderResponseError", "SignerError", "TransportError", "Either partial failures", "error composition", or needs to understand how voltaire-effect handles errors. Use when this capability is needed.
metadata:
  author: cyotee
---

# Voltaire Effect Error Handling

## Overview

In voltaire-effect, errors are values in the type signature rather than hidden exceptions. Every operation declares which errors it can produce, enabling exhaustive handling via `catchTag` and compile-time verification that all error paths are covered.

## Error Types

voltaire-effect defines 12 error categories, all accessible via the `_tag` field:

| Error | Category | When |
|-------|----------|------|
| `ParseError` | Schema | Invalid input to `S.decode` |
| `SignerError` | Signing | Transaction/message signing failures |
| `TransportError` | Transport | HTTP/WebSocket connection issues |
| `ProviderResponseError` | Provider | JSON-RPC response errors |
| `ProviderValidationError` | Provider | Invalid response format |
| `ProviderNotFoundError` | Provider | Missing provider service |
| `ProviderTimeoutError` | Provider | Request timed out |
| `ProviderStreamError` | Provider | Subscription failures |
| `ProviderReceiptPendingError` | Provider | Transaction not yet mined |
| `ProviderConfirmationsPendingError` | Provider | Insufficient confirmations |
| `BlockStreamError` | Streaming | Block polling/subscription failures |
| `CryptoError` | Crypto | Cryptographic operation failures |

## Error Composition

As operations chain, error types accumulate in the type signature:

```typescript
const program = Effect.gen(function* () {
  const addr = yield* S.decode(Address.Hex)(input)
  // Type here: Effect<AddressType, ParseError, never>

  const balance = yield* getBalance(addr)
  // Type here: Effect<bigint, ParseError | GetBalanceError, ProviderService>

  return balance
})
// Final: Effect<bigint, ParseError | GetBalanceError, ProviderService>
```

## Handling Patterns

### Tag-Based Catching

Handle specific errors by their `_tag` discriminator:

```typescript
program.pipe(
  Effect.catchTag('ParseError', (e) =>
    Effect.succeed(0n)  // Fallback for invalid address
  ),
  Effect.catchTag('TransportError', (e) =>
    Effect.fail(new AppError(`Network issue: ${e.message}`))
  )
)
```

### Exhaustive Matching

Use `Match` for comprehensive error handling:

```typescript
import { Match } from 'effect'

const handled = program.pipe(
  Effect.catchAll(
    Match.value.pipe(
      Match.tag('ParseError', () => Effect.succeed(fallback)),
      Match.tag('TransportError', () => retryWithBackoff()),
      Match.tag('ProviderResponseError', () => switchProvider()),
      Match.exhaustive
    )
  )
)
```

### Either for Partial Failures

For batch operations where some items may fail:

```typescript
// Multicall with allowFailure: true
const results = yield* multicall({
  contracts: [call1, call2, call3],
  allowFailure: true
})

// results: Either<Result, Error>[]
const successes = results.filter(Either.isRight)
const failures = results.filter(Either.isLeft)

// Pattern match individual results
results.map(Either.match({
  onLeft: (error) => console.log('Failed:', error),
  onRight: (value) => console.log('Success:', value)
}))
```

**Rule of thumb**: Use `Either` for partial failures in synchronous batches; use `Effect` for retryable async operations.

## Retry and Timeout

Built-in resilience via Effect Schedule:

```typescript
import { Schedule } from 'effect'
import { withTimeout, withRetrySchedule } from 'voltaire-effect'

// Per-request timeout
getBalance(addr).pipe(withTimeout("5 seconds"))

// Exponential backoff with jitter
getBalance(addr).pipe(
  withRetrySchedule(
    Schedule.exponential("500 millis").pipe(
      Schedule.jittered,
      Schedule.compose(Schedule.recurs(3))
    )
  )
)
```

## Transport-Level Defaults

Configure retry and timeout at the transport level for all requests:

```typescript
HttpTransport({
  url: 'https://eth.llamarpc.com',
  timeout: '30 seconds',
  retrySchedule: Schedule.exponential('500 millis').pipe(
    Schedule.jittered,
    Schedule.compose(Schedule.recurs(5))
  )
})
```

## Reference

- Error handling concept: https://voltaire-effect.tevm.sh/concepts/error-handling
- Effect error management: https://effect.website/docs/error-management

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/cyotee) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
