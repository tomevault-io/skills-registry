---
name: brave-search
description: Web search and content extraction via Brave Search API. Use when this capability is needed.
metadata:
  author: hcnimi
---

# Brave Search

Headless web search (and lightweight content extraction) using Brave Search API. No browser required.

## Search

```bash
node {baseDir}/scripts/search.mjs "query"
node {baseDir}/scripts/search.mjs "query" -n 10
node {baseDir}/scripts/search.mjs "query" --content
node {baseDir}/scripts/search.mjs "query" -n 3 --content
```

## Extract a page

```bash
node {baseDir}/scripts/content.mjs "https://example.com/article"
```

Notes:
- Needs `BRAVE_API_KEY`.
- Content extraction is best-effort (good for articles; not for app-like sites).
- If a site is blocked or too JS-heavy, prefer the `summarize` skill (it can use a Firecrawl fallback).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hcnimi) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
