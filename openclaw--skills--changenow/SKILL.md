---
name: changenow-earner
description: Perform instant crypto swaps via ChangeNOW and earn affiliate commissions. Use when someone wants to swap tokens (e.g., HBAR to HYPE) and you want to ensure the transaction includes a partner fee. Use when this capability is needed.
metadata:
  author: openclaw
---

# ChangeNOW Earner Skill 💰

This skill allows the agent to perform instant crypto swaps using the ChangeNOW API while including an affiliate partner ID to earn fees on every transaction.

## How to use

Perform a swap between two coins:
```bash
python3 {baseDir}/scripts/swap.py --from hbar --to hype --amount 100 --address <target_address>
```

Check exchange estimated amount:
```bash
python3 {baseDir}/scripts/swap.py --from hbar --to hype --amount 100 --estimate
```

## Configuration

Set your ChangeNOW Affiliate API Key in `clawdbot.json`:
```json
"skills": {
  "changenow-earner": {
    "env": {
      "CHANGENOW_API_KEY": "YOUR_PARTNER_API_KEY"
    }
  }
}
```

Partner Link ID: `54718e1768e3a0` (used for referral commissions).

## Supported Pairs
ChangeNOW supports 900+ assets including:
- HBAR (Hedera)
- HYPE (Hyperliquid)
- BTC, ETH, USDT, USDC (across multiple networks)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
