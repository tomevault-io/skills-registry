---
name: url-shorten
description: Shorten URLs via tinyurl or bitly API Use when this capability is needed.
metadata:
  author: openclaw
---

# URL Shorten

Shorten URLs via tinyurl or bitly API. Requires `BITLY_TOKEN` env var for bitly; falls back to tinyurl if not set.

## Commands

```bash
# Shorten a URL (uses tinyurl by default, bitly if BITLY_TOKEN is set)
url-shorten "https://example.com/very/long/path/to/resource"
```

## Install

No installation needed. `curl` is always present on the system. Optionally set `BITLY_TOKEN` environment variable to use the bitly API instead of tinyurl.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
