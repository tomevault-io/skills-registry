---
name: searxng-search
description: DEPRECATED - Use web-search skill instead (auto-selects best backend) Use when this capability is needed.
metadata:
  author: devskale
---

# SearXNG Search - DEPRECATED

**This skill is deprecated.** Functionality has been merged into the `web-search` skill which automatically selects the best backend.

## Migration

Use `web-search` instead - it automatically uses SearXNG when credentials are available:

```bash
# Old command
searxng-search "query" --categories images

# New command
web-search "query" --categories images
```

All SearXNG features work through web-search with automatic backend selection.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/devskale) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
