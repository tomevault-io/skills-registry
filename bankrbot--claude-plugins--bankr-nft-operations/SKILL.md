---
name: bankr-dev-nft-operations
description: This skill should be used when building NFT marketplace integrations, implementing collection browsing, or adding floor price tracking. Covers OpenSea integration, floor prices, and NFT purchases. Use when this capability is needed.
metadata:
  author: bankrbot
---

# NFT Operations Capability

Interact with NFT marketplaces via natural language prompts.

## What You Can Do

| Operation | Example Prompt |
|-----------|----------------|
| Search collection | `Find NFTs from Pudgy Penguins` |
| Trending NFTs | `Show me trending NFT collections` |
| Floor price | `What's the floor price for Azuki?` |
| Cheapest listings | `Show the 5 cheapest NFTs in BAYC` |
| Listings under price | `Find NFT listings under 5 ETH in CryptoPunks` |
| Buy floor | `Buy the floor NFT from Pudgy Penguins` |
| Buy by URL | `Buy this NFT: https://opensea.io/...` |
| Buy with budget | `Buy an Azuki NFT under 10 ETH` |
| View holdings | `Show my NFTs` |
| Holdings by chain | `Show my NFTs on Base` |

## Prompt Patterns

```
What's the floor price for {collection}?
Buy the floor NFT from {collection}
Show the {count} cheapest NFTs in {collection}
Buy this NFT: {opensea_url}
Show my NFTs [on {chain}]
```

**Supported chains:** Base, Ethereum, Polygon (and other EVM chains)

## Usage

```typescript
import { execute } from "./bankr-client";

// Check floor price
await execute("What's the floor price for Pudgy Penguins?");

// Buy floor NFT
await execute("Buy the floor NFT from Azuki");

// View holdings
await execute("Show my NFTs on Ethereum");
```

## Related Skills

- `bankr-client-patterns` - Client setup and execute function
- `bankr-api-basics` - API fundamentals
- `bankr-portfolio` - Check ETH balance before purchases

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/bankrbot) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
