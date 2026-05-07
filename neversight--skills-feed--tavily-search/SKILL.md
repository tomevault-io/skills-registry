---
name: tavily-search
description: | Use when this capability is needed.
metadata:
  author: neversight
---

# Tavily Search

Web search optimized for AI agents using Tavily API.

## Usage

```bash
./scripts/search "your search query"
```

## Scripts

| Script | Usage |
|--------|-------|
| `scripts/search <query>` | Search the web |
| `scripts/search "latest AI news" --format json` | JSON output |

## Environment

```bash
export TAVILY_API_KEY="your-api-key"
```

Get API key: https://tavily.com/

## Example

```bash
./scripts/search "Claude AI latest features"
# Returns: Search results optimized for AI context
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
