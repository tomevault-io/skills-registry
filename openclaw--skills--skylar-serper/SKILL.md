---
name: serper
description: Serper.dev API key for Google search Use when this capability is needed.
metadata:
  author: openclaw
---

# Serper.dev Google Search

Search Google via Serper.dev API. Clean, structured results without scraping.

## Quick Start

Requires `SERPER_API_KEY` environment variable.

```javascript
serper_search({
  q: "OpenClaw AI agent",
  num: 5,
  gl: "us",
  hl: "en"
})
```

## Parameters

| Param | Required | Default | Description |
|-------|----------|---------|-------------|
| `q` | Yes | — | Search query |
| `num` | No | 5 | Results count (1-100) |
| `gl` | No | — | Country code (us, uk, th) |
| `hl` | No | — | Language code (en, th) |

## Setup

1. Get API key: https://serper.dev
2. Set environment: `export SERPER_API_KEY=your_key`

## Tool Location

`tools/serper_search.js`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
