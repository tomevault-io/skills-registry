---
name: beszel
description: Monitor home lab servers via Beszel (PocketBase). Use when this capability is needed.
metadata:
  author: openclaw
---

# Beszel Monitoring

Check the status of your local servers.

## Usage
- `beszel status` - Get status of all systems
- `beszel containers` - List top containers by CPU usage

## Commands
```bash
# Get status
source ~/.zshrc && ~/clawd/skills/beszel/index.js status

# Get container stats
source ~/.zshrc && ~/clawd/skills/beszel/index.js containers
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
