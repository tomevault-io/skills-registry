---
name: cacheforge-stats
description: CacheForge terminal dashboard — usage, savings, and performance metrics. See exactly where your tokens go. Use when this capability is needed.
metadata:
  author: openclaw
---

## When to use this skill

Use this skill when the user wants to:
- See their CacheForge usage and savings
- View a terminal dashboard with charts
- Check token reduction rates
- See cost savings breakdown
- Monitor cache performance

## Commands

```bash
# Full terminal dashboard
python3 skills/cacheforge-stats/dashboard.py dashboard

# Usage summary
python3 skills/cacheforge-stats/dashboard.py usage --window 7d

# Breakdown by model/provider/key
python3 skills/cacheforge-stats/dashboard.py breakdown --by model

# Savings-focused view
python3 skills/cacheforge-stats/dashboard.py savings
```

## Environment Variables

- `CACHEFORGE_BASE_URL` — CacheForge API base (default: https://app.anvil-ai.io)
- `CACHEFORGE_API_KEY` — Your CacheForge API key (required)

## API Contract (current)

This skill uses:
- `GET /v1/account/billing`
- `GET /v1/account/info`
- `GET /v1/account/usage`
- `GET /v1/account/usage/breakdown`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
