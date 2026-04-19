---
name: deepbook
description: This skill should be used when the user asks about DeepBook, on-chain order book, CLOB, limit orders, market orders on Sui, or wants to integrate with DeepBook v2/v3. Covers pool management, order placement, balance managers, and trade execution. Use when this capability is needed.
metadata:
  author: mcxross
---

# DeepBook Skill

DeepBook is Sui's native central limit order book (CLOB) DEX. It provides fully on-chain order book functionality with limit orders, market orders, and governance-driven fee parameters.

## When to Use

- User asks about DeepBook, on-chain order book, CLOB on Sui
- User wants to place limit orders or market orders on-chain
- User asks about integrating with DeepBook v2 or v3
- User needs to create pools, manage balance managers, or swap tokens via order book
- User asks about DEEP token staking, governance proposals, or fee structures

## Key Concepts

- **Pool<Base, Quote>**: The central order book for a trading pair
- **BalanceManager**: Holds user funds (base, quote, DEEP) and manages trade authorization
- **TradeProof**: Authorization token for trading operations (from owner, TradeCap, or DepositCap)
- **Order types**: no_restriction (0), immediate_or_cancel (1), fill_or_kill (2), post_only (3)
- **Self-matching options**: self_matching_allowed (0), cancel_taker (1), cancel_maker (2)
- **DEEP token**: Used for fees on whitelisted pools and governance staking

## Package IDs

| Package | ID | Version |
|---------|-----|---------|
| DeepBook v3 | `0x246cbd7d87b1ce5ac9c5112e225be6367380c58be0282179f793c87fde37ffb4` | 1 |
| DEEP Token | `0xdeeb7a4662eec9f2f3def03fb937a663dddaa2e215b8078a284d026b7946c270` | - |
| DeepBook v2 (legacy) | `0xdee9` | - |

## Source Files

Decompiled source at:
`packages/mainnet_most_used/0x24/6cbd7d87b1ce5ac9c5112e225be6367380c58be0282179f793c87fde37ffb4/decompiled_modules/`

Key modules: pool.move, balance_manager.move, order.move, order_info.move, book.move, trade_params.move, registry.move, vault.move, deep_price.move, governance.move, fill.move, history.move, order_query.move, constants.move

## Key Modules

| Module | Purpose |
|--------|---------|
| `pool` | Main entry point: create pools, place/cancel orders, swaps, staking, governance |
| `balance_manager` | Manage user funds, deposit/withdraw, trade authorization via caps |
| `order` | Individual order representation and accessors |
| `order_info` | Detailed order execution info including fills |
| `order_query` | Paginated order book queries |
| `book` | Internal order book data structure (bids/asks) |
| `trade_params` | Fee parameters (taker/maker fees, stake required) |
| `vault` | Holds actual token balances for pools |
| `registry` | Global pool registry and admin operations |
| `governance` | On-chain governance for fee proposals |
| `deep_price` | DEEP token pricing for fee conversion |
| `fill` | Individual fill event during order matching |
| `history` | Per-epoch volume and fee statistics |
| `constants` | Protocol constants (order types, statuses, fees) |

## Common Patterns

### Simple Token Swap (No BalanceManager)
```move
let (remaining_base, received_quote, remaining_deep) = pool::swap_exact_base_for_quote<SUI, USDC>(
    &mut pool, base_coin, deep_coin, min_quote_out, &clock, ctx
);
```

### Place a Limit Order
```move
let balance_manager = balance_manager::new(ctx);
balance_manager::deposit<SUI>(&mut balance_manager, sui_coin, ctx);
balance_manager::deposit<DEEP>(&mut balance_manager, deep_coin, ctx);
let proof = balance_manager::generate_proof_as_owner(&mut balance_manager, ctx);
let order_info = pool::place_limit_order<SUI, USDC>(
    &mut pool, &mut balance_manager, &proof,
    1,       // client_order_id
    0,       // order_type: no_restriction
    0,       // self_matching: allowed
    1_000_000_000, // price
    1_000_000_000, // quantity
    false,   // is_bid (sell)
    true,    // pay_with_deep
    18446744073709551615, // never expire
    &clock, ctx
);
```

### Key Constants
- Pool creation fee: 500 DEEP
- Float scaling: 1e9 (all prices/fees use 9-decimal fixed-point)
- DEEP unit: 1e6 (6 decimals)
- Max fills per order: 100
- Max open orders per manager: 100

## Related Skills
- `sui-framework` -- Core Coin, Balance, Clock types used by DeepBook
- `cetus` / `turbos` -- Alternative DEX protocols
- `pyth` -- Oracle prices referenced by some DeepBook integrations

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mcxross) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
