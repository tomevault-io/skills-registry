---
name: clawd-market
description: Agent-native marketplace for Claude Code skills with x402 payments Use when this capability is needed.
metadata:
  author: csmoove530
---

# CLAWD Market

The agent-native marketplace for Claude Code skills. Just describe what you need.

## Usage

Simply tell Claude what capability you're looking for:

- "I need a skill that helps me write better tests"
- "Find me something for database migrations"
- "I want to automate my deployment process"

CLAWD Market will find, recommend, and install the best skill for you.

## How It Works

1. **Discovery**: Tell Claude what you need in natural language
2. **Recommendation**: AI searches the catalog and recommends the best match
3. **Purchase**: Confirm with "yes" and payment flows via x402 + CLAWD wallet
4. **Installation**: Skill is automatically downloaded and installed

## Payment

CLAWD Market uses the x402 payment protocol with USDC on Base L2. Payments are instant and split:
- 99% to skill creator
- 1% to CLAWD Market

## For Skill Sellers

Use `/clawd publish` to list your skills on the marketplace.

## Requirements

- Claude Code with skills support
- CLAWD embedded wallet with USDC balance
- Internet connection

## API Endpoint

Configure the marketplace API endpoint in your `.clawd-market` config file:

```json
{
  "apiEndpoint": "https://api.clawdmarket.com/api/v1"
}
```

Or set via environment variable:
```bash
export CLAWD_MARKET_API="https://api.clawdmarket.com/api/v1"
```

For local development:
```json
{
  "apiEndpoint": "http://localhost:3000/api/v1"
}
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/csmoove530) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
