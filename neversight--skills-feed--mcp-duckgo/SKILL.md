---
name: mcp-duckgo
description: Skills for web search and content scraping via DuckDuckGo MCP Server. Used when users need online searching and web scraping. Use when this capability is needed.
metadata:
  author: neversight
---

# DuckDuckGo Search
Executing Shell commands.

## Web search
- `npx -y mcporter call --stdio 'uvx duckduckgo-mcp-server' search query="{keyword}" max_results=10`

## Web fetch
- `npx -y mcporter call --stdio 'uvx duckduckgo-mcp-server' fetch_content url="https://..."`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
