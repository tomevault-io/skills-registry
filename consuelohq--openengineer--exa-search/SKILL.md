---
name: exa-search
description: Search the web using Exa AI instead of Brave (MCP-compatible endpoint) Use when this capability is needed.
metadata:
  author: consuelohq
---

# Exa Search

Search the web using Exa AI. Use this **INSTEAD of** the built-in `web_search` tool (which uses Brave).

## Quick Start

```bash
# Set your API key (one-time setup)
export EXA_API_KEY="your_key_here"  # Add to ~/.openclaw/.env for persistence

# Run a search
~/.openclaw/workspace/skills/exa-search/search.sh "your query" 5
```

## Get API Key

1. Go to https://dashboard.exa.ai/api-keys
2. Create a new API key
3. Set it in your environment: `EXA_API_KEY=...`

## Why Exa?

- **Semantic search** - understands meaning, not just keywords
- **Better for AI/tech content** - designed for LLM workflows  
- **Clean results** - structured, relevant output
- **MCP-ready** - uses Model Context Protocol (future-proof)

## Comparison

| Feature | Exa | Brave |
|---------|-----|-------|
| Semantic search | ✅ | ❌ |
| Real-time web | ✅ | ✅ |
| Code/examples focus | ✅ | ⚠️ |
| Free tier | ✅ | ✅ |
| MCP support | ✅ | ❌ |

## MCP Server

The full Exa MCP server is available at `https://mcp.exa.ai/mcp`. 
When OpenClaw adds native MCP support, this skill will connect directly.

For now, use the `search.sh` script which calls the Exa REST API.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/consuelohq) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
