---
name: masumi-network
description: Masumi Network skill for warranty vault verification. Handles OCR receipt scanning, Cardano blockchain proof-of-purchase logging, immutable decision logging, agent collaboration discovery, and smart wallet payments. Use for warranty claims, product verification, agent-to-agent service payments, or immutable audit trails on Cardano. Use when this capability is needed.
metadata:
  author: openclaw
---

# Masumi Network Warranty Vault

## Quick Start
Run scripts for core functions:
- `scripts/register-agent.py` - Register Franz AI
- `scripts/verify-warranty.py --receipt "Warranty text"` - Verify receipt
- `scripts/collaborate.py` - Find partners

## Workflow
1. OCR receipt → Extract data
2. Generate proof-hash
3. Log to Cardano TX
4. Charge fee via smart wallet

See references/api.md for endpoints.

## Monetization
- 1% fee per verification
- Publish to ClawHub for passive income

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
