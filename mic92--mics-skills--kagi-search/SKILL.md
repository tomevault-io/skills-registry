---
name: kagi-search
description: Search the web using Kagi. Use for web searches with Quick Answer AI summaries. Use when this capability is needed.
metadata:
  author: mic92
---

# Usage

```bash
# Basic search (shows Quick Answer summary only)
kagi-search "what is the capital of France"

# Include search result links (default: 3 links)
kagi-search -l "search query"

# Include more links
kagi-search -l -n 10 "search query"

# JSON output for parsing
kagi-search -j "search query" | jq '.results[0].url'
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mic92) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
