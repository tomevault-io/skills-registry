---
name: product-hunt-launch
description: Track your Product Hunt launch stats (Rank, Upvotes, Comments) in real-time via CLI. Use when this capability is needed.
metadata:
  author: openclaw
---

# Product Hunt Launch 🚀

Track your launch day metrics from the terminal.

## Setup

1. Get a Developer Token from [Product Hunt API Dashboard](https://www.producthunt.com/v2/oauth/applications).
2. Set it: `export PH_API_TOKEN="your_token"`

## Commands

### Check Post Stats
```bash
ph-launch stats --slug "your-product-slug"
# Output: Rank #4 | 🔼 450 | 💬 56
```

### Monitor Launch (Live Dashboard)
```bash
ph-launch monitor --slug "your-product-slug" --interval 60
```

### List Today's Leaderboard
```bash
ph-launch leaderboard
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
