---
name: helius-api
description: > Use when this capability is needed.
metadata:
  author: openclaw
---

# Helius API

Query comprehensive Solana data via REST endpoints. Requires `HELIUS_API_KEY` env var.

## Setup

```bash
export HELIUS_API_KEY="your-key-here"
```

Get a key at <https://dashboard.helius.dev>

## Base URLs

- **Wallet API:** `https://api.helius.xyz/v1/wallet/{address}/...?api-key=KEY`
- **Enhanced Transactions:** `https://api-mainnet.helius-rpc.com/v0/...?api-key=KEY`

Auth: `?api-key=$HELIUS_API_KEY` query param or `X-Api-Key` header.

## Wallet API Endpoints

| Endpoint | Path | Description |
|----------|------|-------------|
| Balances | `/v1/wallet/{address}/balances` | Token + NFT holdings with USD values |
| History | `/v1/wallet/{address}/history` | Parsed transaction history with balance changes |
| Transfers | `/v1/wallet/{address}/transfers` | Token transfer activity (sent/received) |
| Identity | `/v1/wallet/{address}/identity` | Known wallet labels (exchanges, protocols) |
| Batch Identity | `/v1/wallet/batch-identity` (POST) | Look up up to 100 addresses at once |
| Funded By | `/v1/wallet/{address}/funded-by` | Original funding source of a wallet |

## Enhanced Transactions Endpoints

| Endpoint | Path | Description |
|----------|------|-------------|
| Parse Transactions | `/v0/transactions/` (POST) | Parse signatures into human-readable data |
| Transaction History | `/v0/addresses/{address}/transactions` | Enhanced tx history with type/time/slot filtering |

## Reference Files

Read the appropriate file for detailed parameters, response formats, and examples:

- **Balances** (portfolio, holdings, USD values): See [references/balances.md](references/balances.md)
- **History** (wallet tx history, P&L, tax reports): See [references/history.md](references/history.md)
- **Transfers** (sent/received, payment tracking): See [references/transfers.md](references/transfers.md)
- **Identity** (wallet labels, exchange detection): See [references/identity.md](references/identity.md)
- **Funded By** (funding source, sybil detection): See [references/funded-by.md](references/funded-by.md)
- **Enhanced Transactions** (parse tx, enhanced history): See [references/enhanced-transactions.md](references/enhanced-transactions.md)

## Implementation Notes

- Use `curl` or `fetch` — no SDK required
- All endpoints return JSON
- Pagination: use `page` param (balances) or `before`/cursor (history, transfers)
- Default limit: 100 per request
- Wallet API requests cost 100 credits each
- Identity returns 404 for unknown wallets — handle gracefully
- Funded By returns 404 if wallet never received SOL
- Enhanced Transactions uses a different base URL (`api-mainnet.helius-rpc.com`)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
