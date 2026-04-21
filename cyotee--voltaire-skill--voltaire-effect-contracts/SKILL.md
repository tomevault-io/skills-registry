---
name: voltaire-effect-contract-interactions
description: This skill should be used when the user asks about "voltaire-effect contract", "Contract factory", "readContract", "writeContract", "simulateContract", "type-safe ABI", "ERC-20 Effect", "contract read write", "contract events", "voltaire-effect multicall", or needs to understand how voltaire-effect interacts with smart contracts. Use when this capability is needed.
metadata:
  author: cyotee
---

# Voltaire Effect Contract Interactions

## Overview

The `Contract()` factory creates type-safe contract instances that infer argument and return types directly from your ABI definition. Read operations require `ProviderService`; write operations additionally require `SignerService`.

## Contract Factory

```typescript
import { Contract, Provider, HttpTransport } from 'voltaire-effect'

const erc20Abi = [
  {
    type: 'function', name: 'balanceOf',
    inputs: [{ name: 'account', type: 'address' }],
    outputs: [{ type: 'uint256' }],
    stateMutability: 'view'
  },
  {
    type: 'function', name: 'transfer',
    inputs: [{ name: 'to', type: 'address' }, { name: 'amount', type: 'uint256' }],
    outputs: [{ type: 'bool' }],
    stateMutability: 'nonpayable'
  }
] as const  // ← 'as const' required for type inference

const program = Effect.gen(function* () {
  const usdc = yield* Contract('0xA0b86991c6218b36c1d19D4a2e9Eb0cE3606eB48', erc20Abi)
  return usdc
})
```

## Read Operations

Query view/pure functions via `eth_call` (no wallet needed):

```typescript
const getBalance = Effect.gen(function* () {
  const usdc = yield* Contract(usdcAddress, erc20Abi)
  const balance = yield* usdc.read.balanceOf(userAddress)
  return balance  // bigint
})

// Provide only ProviderService
const ProviderLayer = Provider.pipe(
  Layer.provide(HttpTransport('https://eth.llamarpc.com'))
)
const balance = await Effect.runPromise(getBalance.pipe(Effect.provide(ProviderLayer)))
```

## Write Operations

State-changing functions require SignerService:

```typescript
import { Secp256k1Live } from 'voltaire-effect/crypto/Secp256k1'
import { KeccakLive } from 'voltaire-effect/crypto/Keccak256'

const transfer = Effect.gen(function* () {
  const usdc = yield* Contract(usdcAddress, erc20Abi)
  const txHash = yield* usdc.write.transfer(recipientAddress, 1000000n)
  return txHash
})

// Full layer stack with crypto + signer
const WriteLayer = Layer.mergeAll(
  ProviderLayer,
  Secp256k1Live,
  KeccakLive,
  SignerLayer
)

await Effect.runPromise(transfer.pipe(Effect.provide(WriteLayer)))
```

## Simulate Before Sending

Validate a write operation without actually sending:

```typescript
const program = Effect.gen(function* () {
  const usdc = yield* Contract(usdcAddress, erc20Abi)
  const { result, request } = yield* usdc.simulate.transfer(recipient, amount)
  // result: true (simulated return value)
  // request: prepared transaction request
  return result
})
```

## Event Queries

Query historical contract events:

```typescript
const program = Effect.gen(function* () {
  const usdc = yield* Contract(usdcAddress, erc20Abi)
  const transfers = yield* usdc.getEvents({
    eventName: 'Transfer',
    fromBlock: 18000000n,
    toBlock: 'latest'
  })
  return transfers
})
```

## Multicall Batching

Batch multiple contract reads into a single network call via Multicall3:

```typescript
import { multicall } from 'voltaire-effect'

const program = Effect.gen(function* () {
  const results = yield* multicall({
    contracts: [
      { address: usdc, abi: erc20Abi, functionName: 'balanceOf', args: [user] },
      { address: dai, abi: erc20Abi, functionName: 'balanceOf', args: [user] },
      { address: weth, abi: erc20Abi, functionName: 'balanceOf', args: [user] },
    ],
    allowFailure: true  // Returns Either[] for partial failures
  })
  return results
})
```

With `allowFailure: true`, results are `Either<Result, Error>[]` - some calls can fail without aborting the batch.

## Type Safety

The ABI definition drives compile-time type checking:

```typescript
const usdc = yield* Contract(address, erc20Abi)

// Correct - matches ABI
usdc.read.balanceOf('0x...')  // ✓ address argument

// Wrong - TypeScript compile error
usdc.read.balanceOf(42)       // ✗ number is not address
usdc.read.nonExistent()       // ✗ function not in ABI
```

## Per-Request Configuration

```typescript
import { withTimeout, withRetrySchedule } from 'voltaire-effect'

// Timeout on a contract read
const balance = yield* usdc.read.balanceOf(addr).pipe(
  withTimeout("5 seconds")
)

// Retry a write
const txHash = yield* usdc.write.transfer(to, amount).pipe(
  withRetrySchedule(Schedule.exponential("500 millis").pipe(
    Schedule.jittered,
    Schedule.compose(Schedule.recurs(3))
  ))
)
```

## Error Handling

```typescript
program.pipe(
  Effect.catchTag('ContractCallError', (e) =>
    // Read/simulate failure (revert, invalid args, etc.)
    Effect.fail(new AppError(`Contract read failed: ${e.message}`))
  ),
  Effect.catchTag('ContractWriteError', (e) =>
    // Write failure (insufficient gas, nonce conflict, etc.)
    Effect.fail(new AppError(`Contract write failed: ${e.message}`))
  )
)
```

## Real-World Example: Uniswap V2 Swap

```typescript
const uniV2Router = yield* Contract(routerAddress, uniswapV2RouterAbi)

// 1. Get price quote
const amounts = yield* uniV2Router.read.getAmountsOut(
  amountIn,
  [tokenA, tokenB]
)

// 2. Calculate minimum output with slippage
const minOut = amounts[1] * 995n / 1000n  // 0.5% slippage

// 3. Execute swap
const txHash = yield* uniV2Router.write.swapExactTokensForTokens(
  amountIn,
  minOut,
  [tokenA, tokenB],
  recipient,
  BigInt(Math.floor(Date.now() / 1000) + 1200)  // 20 min deadline
)
```

## Related Services

| Service | Purpose |
|---------|---------|
| `ContractRegistryService` | Manage multiple contract instances |
| `ExplorerContracts` | Fetch ABIs dynamically from block explorers |

## Reference

- Contract docs: https://voltaire-effect.tevm.sh/services/contract
- Multicall docs: https://voltaire-effect.tevm.sh/services/multicall
- Examples: https://voltaire-effect.tevm.sh/examples/contract-interactions

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/cyotee) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
