---
name: mem
description: Search local memory index (local-first). Use for /mem queries in Telegram. Use when this capability is needed.
metadata:
  author: openclaw
---

# Memory Search (/mem)

## Overview

Run local-first memory search using the cached index.

## Usage

1) Update the index if needed:
```bash
scripts/index-memory.py
```

2) Search the index with the user query:
```bash
scripts/search-memory.py "<query>" --top 5
```

## Output

Return the top hits with their paths and headers. Summarize briefly if needed.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
