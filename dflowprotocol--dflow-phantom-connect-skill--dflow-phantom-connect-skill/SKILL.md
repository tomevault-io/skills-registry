---
name: phantom-wallet-mcp
description: > Use when this capability is needed.
metadata:
  author: DFlowProtocol
---

# Phantom Wallet MCP

Use the `phantom` MCP server to interact with the user's Phantom wallet directly — no SDK integration code needed.

## Setup

Add the Phantom MCP server to your MCP configuration:

```json
{
  "mcpServers": {
    "phantom": {
      "command": "npx",
      "args": ["-y", "@phantom/mcp-server"],
      "env": {
        "PHANTOM_APP_ID": "<your-app-id>"
      }
    }
  }
}
```

Get your `PHANTOM_APP_ID` from [Phantom Portal](https://phantom.com/portal/login). On first use, the server opens a browser for OAuth authentication via Google or Apple login.

## Available Tools

| Tool | Description |
|------|-------------|
| `get_wallet_addresses` | Get blockchain addresses (Solana, Ethereum, Bitcoin, Sui) for the connected wallet |
| `transfer_tokens` | Transfer SOL or SPL tokens on Solana — builds, signs, and sends the transaction |
| `buy_token` | Fetch Solana swap quotes from Phantom API; optionally sign and send |
| `sign_transaction` | Sign a transaction (base64url for Solana, RLP hex for Ethereum) |
| `sign_message` | Sign a UTF-8 message with automatic chain-specific routing |

## Supported Networks

| Chain | Networks | CAIP-2 Examples |
|-------|----------|-----------------|
| Solana | mainnet, devnet, testnet | `solana:mainnet`, `solana:devnet` |
| Ethereum | Mainnet, Sepolia, Polygon, Base, Arbitrum | `eip155:1`, `eip155:137` |
| Bitcoin | Mainnet | `bip122:000000000019d6689c085ae165831e93` |
| Sui | Mainnet, Testnet | `sui:mainnet` |

## Examples

### Get wallet addresses

Retrieve the user's wallet addresses across all supported chains:

```
Use the phantom MCP's get_wallet_addresses tool to list the user's addresses.
```

Returns addresses for each connected chain (Solana, Ethereum, Bitcoin, Sui).

### Transfer SOL

Send SOL to a recipient address:

```
Use the phantom MCP's transfer_tokens tool:
- token: "SOL"
- recipientAddress: "<recipient>"
- amount: "0.1"
- network: "solana:mainnet"
```

The MCP handles transaction building, signing, and submission.

### Transfer SPL tokens

Send any SPL token by its mint address:

```
Use the phantom MCP's transfer_tokens tool:
- token: "<token-mint-address>"
- recipientAddress: "<recipient>"
- amount: "10"
- network: "solana:mainnet"
```

### Buy / swap tokens

Get a swap quote and optionally execute it:

```
Use the phantom MCP's buy_token tool:
- tokenMint: "<token-mint-to-buy>"
- amount: "1000000"  (in lamports for SOL input)
- network: "solana:mainnet"
```

### Sign a message

Sign a UTF-8 message for verification or authentication:

```
Use the phantom MCP's sign_message tool:
- message: "Hello, verifying ownership"
- network: "solana:mainnet"
```

The MCP routes to the correct chain-specific signing based on the network parameter.

## Important Notes

- **Preview software** — use a separate wallet with minimal funds for testing
- Sessions persist locally in `~/.phantom-mcp/session.json`
- The MCP server runs via stdio transport (launched by `npx -y @phantom/mcp-server`)
- Token transfers on Solana support both native SOL and any SPL token
- Swap quotes come from Phantom's API and include fee and slippage info

---
> Source: [DFlowProtocol/dflow_phantom-connect-skill](https://github.com/DFlowProtocol/dflow_phantom-connect-skill) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-18 -->
