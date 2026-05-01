---
name: bailian-web-search
description: AI-optimized web search via Bailian(Alibaba ModelStdio) API. Returns multisourced, concise web search results for LLMs. Use when this capability is needed.
metadata:
  author: openclaw
---

# Bailian Web Search

AI-optimized web search using Bailian WebSearch(Enable_search) API. Designed for AI agents - returns clean, relevant content.

## Search

```bash
{baseDir}/scripts/mcp-websearch.sh "query"
{baseDir}/scripts/mcp-websearch.sh  "query"  10
```

## Options

- `<count>`: Number of results (default: 5, max: 20)
- `<query>`: User Query for Websearch

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
