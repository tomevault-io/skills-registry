---
name: sushiswap-sdk
description: > Use when this capability is needed.
metadata:
  author: openclaw
---

# SushiSwap SDK Integration

The SushiSwap SDK is a **TypeScript wrapper** around the SushiSwap API.
It provides **strongly typed primitives** and utilities for working with
tokens, prices, swap quotes, and transaction generation.

This SDK does **not** replace the API — it builds on top of it with safer,
more expressive abstractions.

---

## Installation

Install the required packages using your package manager of choice:

```bash
pnpm add sushi viem
```

```bash
npm add sushi viem
```

```bash
yarn add sushi viem
```

```bash
bun add sushi viem
```

---

## How To Use

1. Import the appropriate SushiSwap SDK helpers from `sushi/evm`
2. Select the correct SDK method based on user intent:
    - Swap quote → `getQuote()`
    - Swap execution → `getSwap()`
3. Provide all required parameters exactly as defined by the SDK types
4. **Always include a valid `referrer` value**
5. Validate inputs (chainId, token addresses, amount, slippage) before execution
6. Use returned transaction data exactly as provided for simulation or execution

The SDK is a thin wrapper over the SushiSwap REST API — all routing, pricing, and calldata generation is still performed by the API.

---

## Supported Networks

The SushiSwap SDK exposes the list of supported swap networks via:

```ts
import { SWAP_API_SUPPORTED_CHAIN_IDS } from 'sushi/evm'
```

- Agents and integrators should always check this list before attempting to:
    - Fetch a quote
    - Generate swap transaction data
- If a requested chainId is not included:
    - The agent must fail early or prompt the user to select a supported network
    - Agents must not attempt to guess or hardcode supported chains

This list reflects the networks currently supported by the SushiSwap Aggregator API. The supported networks may change over time and should not be cached indefinitely.

--

## Mandatory `referrer` Parameter

- The `referrer` parameter **must be specified** when calling `getQuote()` or `getSwap()`
- The agent or integrator **must identify themselves** using this field
- Swap-related SDK calls must not be executed without a `referrer` value
- The SDK must not auto-generate or omit this value on behalf of the integrator

---

## Fee Customization

The SushiSwap SDK supports customized swap fees when using `getQuote()` or `getSwap()`.

### Default fee model

- Swap-related requests follow an **80/20 fee split by default**
    - **80%** to the integrator (referrer)
    - **20%** to SushiSwap
- This split applies unless explicitly overridden by SushiSwap

### Custom fee splits

- Alternative fee splits require a **partnership** with SushiSwap
- Agents and integrators should not assume custom splits are available. If users request alternative fee splits, agents should direct them to the SushiSwap
team rather than attempting to modify request parameters.

---

## Additional Reference

For detailed SDK examples & execution flow, see:

[references/REFERENCE.md](references/REFERENCE.md)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
