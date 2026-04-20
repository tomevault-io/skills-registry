---
name: interacting-with-polymarket
description: Manages interactions with the Polymarket API (CLOB/Gamma) to fetch markets, odds, and order books. Use when the user wants to get betting data or trade programmatically.
metadata:
  author: kevingastelum
---

# Interacting with Polymarket

## When to use this skill
- User asks to "fetch markets" or "get odds" from Polymarket.
- User needs to understand the Polymarket CLOB (Central Limit Order Book) API.
- User wants to check liquidity, volume, or open interest for specific events.
- Debugging API authentication or rate limits related to Polymarket.

## Workflow
- [ ] **Identify Endpoint**: Determine if we need Basic Market Data (Gamma API) or Trading/Orderbook Data (CLOB API).
- [ ] **Auth Check**: If trading or fetching private data, verify `POLY_API_KEY`, `POLY_SECRET`, and `POLY_PASSPHRASE` are in `.env`.
- [ ] **Fetch**: Use the `worker` (Go) for high-performance scraping or `model` (Python) for ad-hoc analysis.
- [ ] **Parse**: Ensure types match the strict schemas in `worker/internal/polymarket/types.go` (if exists) or create them.

## Instructions

### 1. API Selection Heuristic
*   **Gamma API (GraphQL/REST)**: Use for discovering markets, getting historical prices, and reading resolved states. No auth usually required for public data.
    *   Endpoint: `https://gamma-api.polymarket.com/query`
*   **CLOB API**: Use for placing orders, getting precise order book depth, and account balances.
    *   Endpoint: `https://clob.polymarket.com/`

### 2. Common Operations (Go Example)
When implementing in the `worker`:

```go
// Construct the request with proper headers
req, _ := http.NewRequest("GET", "https://clob.polymarket.com/markets/" + tokenID, nil)
req.Header.Add("Content-Type", "application/json")
// ... handle response
```

### 3. Data Structures
Polymarket uses large integers for token amounts (Wei). Always handle decimals carefully.
- **TokenID**: String (hex)
- **OutcomeIndex**: Integer (0 or 1 usually for Binary markets)

## Resources
- [Polymarket CLOB API Docs](https://docs.polymarket.com/)
- [Gamma API Docs](https://docs.polymarket.com/#gamma-api)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kevingastelum) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
