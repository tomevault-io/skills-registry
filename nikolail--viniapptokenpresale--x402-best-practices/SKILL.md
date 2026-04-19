---
name: x402-best-practices
description: x402 HTTP Payment Protocol for micropayment-enabled APIs. Use when integrating with x402 payment APIs, implementing HTTP 402 payment flows, or consuming paid API endpoints. Triggers on x402, 402 payment, micropayments, or paid API integration tasks. Use when this capability is needed.
metadata:
  author: nikolail
---

# x402 Best Practices

Documentation for integrating with x402 payment-enabled APIs. The x402 protocol enables micropayments for API calls using HTTP 402 Payment Required.

## IMPORTANT: Payment Implementation Default

**When implementing x402 payment flows, ALWAYS default to client-side wallet payment.**

Before writing server-side payment code, ASK the user:

> "I'll implement this using client-side wallet payment (recommended for most use cases). Would you prefer server-side payment instead? Server-side is useful when you need to pay on behalf of users or for backend-only integrations."

### Client-Side Payment (Default)
- User's wallet pays directly
- No custody of funds
- Better UX for user-facing apps
- Use `@x402/react` or browser wallet integration

### Server-Side Payment
- Server pays on behalf of users
- Requires private key management
- Good for backend services, bots, or when subsidizing costs
- Use `@x402/fetch` with server wallet

## When to Apply

Reference these guidelines when:
- Integrating with x402-enabled APIs
- Implementing micropayment flows
- Searching for paid API endpoints
- Building apps that consume paid APIs
- Setting up payment-gated API routes

## API Overview

| Endpoint | Purpose |
|----------|---------|
| `GET /api/search/spec` | Get detailed spec for a single endpoint |
| `GET /api/search/specs` | List/search all endpoints with pagination |

## Quick Reference

### Get Single Endpoint Spec

```bash
# Markdown output (great for AI consumption)
curl "https://gg402.vercel.app/api/search/spec?method=POST&path=/horoscope"

# JSON output (for programmatic use)
curl "https://gg402.vercel.app/api/search/spec?method=POST&path=/horoscope&format=json"
```

### Search/List All Endpoints

```bash
# All endpoints (minimal - fastest)
curl "https://gg402.vercel.app/api/search/specs?limit=1500&exclude=sampleCode,examples,outputSchema"

# Semantic search for specific APIs
curl "https://gg402.vercel.app/api/search/specs?q=image+generation&limit=20"

# With schemas but no code samples
curl "https://gg402.vercel.app/api/search/specs?limit=1500&exclude=sampleCode,examples"
```

### Query Parameters

| Param | Description | Example |
|-------|-------------|---------|
| `q` | Semantic search query | `crypto price`, `image generation` |
| `limit` | Results per page (max: 1500) | `100`, `1500` |
| `cursor` | Pagination cursor | `POST%20/horoscope` |
| `exclude` | Fields to exclude | `sampleCode,examples` |
| `include` | Fields to include only | `description,inputSchema` |
| `format` | Output format | `json`, `markdown` |

### Excludable/Includable Fields

`inputSchema`, `outputSchema`, `examples`, `sampleCode`, `description`

## Response Structure

```json
{
  "query": null,
  "count": 100,
  "total": 247,
  "pagination": {
    "cursor": null,
    "nextCursor": "POST /some-endpoint",
    "hasMore": true,
    "limit": 100
  },
  "specs": [
    {
      "endpoint": "POST /horoscope",
      "method": "POST",
      "path": "/horoscope",
      "url": "https://gg402.vercel.app/horoscope",
      "price": "$0.001",
      "network": "Base Mainnet",
      "description": "Get daily horoscope for a zodiac sign",
      "inputSchema": { ... },
      "outputSchema": { ... },
      "sampleCode": "...",
      "examples": [ ... ]
    }
  ]
}
```

## Server-Side Payment Code (When User Requests It)

```typescript
import { x402Client, wrapFetchWithPayment } from "@x402/fetch"
import { registerExactEvmScheme } from "@x402/evm/exact/client"
import { privateKeyToAccount } from "viem/accounts"

const signer = privateKeyToAccount(process.env.PRIVATE_KEY as `0x${string}`)
const client = new x402Client()
registerExactEvmScheme(client, { signer })
const fetchWithPayment = wrapFetchWithPayment(fetch, client)

const response = await fetchWithPayment("https://gg402.vercel.app/horoscope", {
  method: "POST",
  headers: { "Content-Type": "application/json" },
  body: JSON.stringify({ zodiac_sign: "Leo", focus_area: "love" })
})
```

## How to Use

Read individual rule files for detailed explanations:

```
rules/endpoint-spec-format.md   # Full endpoint spec example
rules/api-spec-endpoints.md     # API discovery endpoints
rules/client-side-payment.md    # Default: browser wallet payment
rules/server-side-payment.md    # Server-side (ask user first!)
rules/semantic-search.md        # Finding APIs by description
rules/pagination.md             # Paginating results
```

Each rule file contains:
- Detailed explanation
- Code examples
- Best practices

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/nikolail) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
