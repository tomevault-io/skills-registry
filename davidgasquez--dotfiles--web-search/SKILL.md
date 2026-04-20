---
name: web-search
description: Generic web search. Use when you need fast, headless web search. Use when this capability is needed.
metadata:
  author: davidgasquez
---

# Web Search

Search the web in a headless and fast way.

## Default workflow

1. Run `web-search "<query>"` (with a large timeout like 60s)
2. Open or fetch (`markdown-fetch` skill) the most relevant pages as needed
3. Merge, deduplicate, and use the findings accordingly
4. Repeat if needed

Example:

```bash
web-search "latest stable Rust release and best sources"
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/davidgasquez) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
