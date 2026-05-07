---
name: tool-polymarket-trades
description: Use the polymarket_trades tool to fetch and filter recent Polymarket trades (free Data API), optionally by market or wallet. Use when this capability is needed.
metadata:
  author: neversight
---

# polymarket_trades (Polymarket trade tape)

## When to use

- See recent trade flow for a specific market (or wallet).
- Detect whale activity (combine with `minSize`).

## Parameters

- `marketId` (string, optional):
  - Gamma numeric id like `"516710"` **or**
  - conditionId hex like `"0x..."` (64 hex chars).
  - If numeric, the tool resolves it to conditionId via Gamma.
- `user` (string, optional): Wallet address (`0x...`) to filter trades.
- `limit` (int, optional, 1–500): Default 50.
- `minSize` (number, optional): Filters out trades smaller than this (USD size from API).

## Examples

By market:
```json
{ "name": "polymarket_trades", "params": { "marketId": "516710", "limit": 200, "minSize": 250 } }
```

By wallet:
```json
{ "name": "polymarket_trades", "params": { "user": "0xabc123...", "limit": 100 } }
```

## Output

- Returns: `{ marketId, conditionId, user, trades: Array<{ts,wallet,side,outcome,price,size,title,url,conditionId,tx}> }`
- Rendered:
  - `Meta` (text: marketId/conditionId/user/trades count)
  - `Trades` (table: ts/side/outcome/price/size/wallet/title)

## Notes

- If both `user` and `marketId` are provided, the API returns trades matching both filters.
- `targetWindow`: `poly`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
