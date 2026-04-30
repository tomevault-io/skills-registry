---
name: gumroad-admin
description: Gumroad Admin CLI. Check sales, products, and manage discounts. Use when this capability is needed.
metadata:
  author: duclm1x1
---

# Gumroad Admin

Manage your Gumroad store from OpenClaw.

## Setup

1. Get your Access Token from Gumroad (Settings > Advanced > Applications).
2. Set it: `export GUMROAD_ACCESS_TOKEN="your_token"`

## Commands

### Sales
```bash
gumroad-admin sales --day today
gumroad-admin sales --last 30
```

### Products
```bash
gumroad-admin products
```

### Discounts
```bash
gumroad-admin discounts create --product <id> --code "TWITTER20" --amount 20 --type percent
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/duclm1x1) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
