---
name: is-token-safe
description: Checks whether a given crypto token is potentially malicious or risky. Use when this capability is needed.
metadata:
  author: openclaw
---
# IsTokenSafe

Checks whether a given crypto token is potentially malicious or risky.

## What it does
- Analyzes token contract metadata
- Flags common scam or rug-pull patterns
- Provides a quick safety signal for trading bots and agents

## Use cases
- Automated trading bots
- Polymarket / prediction market agents
- Token discovery pipelines

## Input
- Token address

## Output
- Risk level (low / medium / high)
- Reason summary

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
