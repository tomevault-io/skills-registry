---
name: search-hybrid
description: Hybrid web search using both Perplexity and SearXNG. Use when you need cross-verified search results, want to compare sources, or need privacy-focused local search. Perplexity provides AI-synthesized answers with citations. SearXNG provides raw results from multiple engines without tracking. Use when this capability is needed.
metadata:
  author: straff2002
---

# Hybrid Search Skill

This skill provides access to both Perplexity AI and SearXNG meta-search engine. Use this when you need comprehensive, cross-verified research.

## When to Use Each

### Perplexity
- AI-synthesized answers with source citations
- Complex multi-step reasoning
- Current events and recent information
- When you need a definitive answer

### SearXNG
- Privacy-focused (no tracking)
- Raw results from multiple search engines
- When Perplexity API is unavailable
- Broad coverage without AI synthesis

### Hybrid Mode (Both)
- Cross-verify important facts
- Compare AI synthesis vs raw sources
- Maximum coverage for research tasks

## Usage

### Perplexity Search
```bash
# Simple query
scripts/perplexity_search.sh "What is the capital of France?"

# With reasoning model
scripts/perplexity_search.sh "Explain quantum computing" -m sonar-reasoning

# Deep research
scripts/perplexity_search.sh "AI agent frameworks comparison 2025" -m sonar-research-pro -c high
```

### SearXNG Search
```bash
# Local SearXNG instance (default)
scripts/searxng_search.sh "Python async best practices"

# Custom SearXNG instance
SEARXNG_URL="https://searx.be/search" scripts/searxng_search.sh "query"
```

### Hybrid Cross-Compare
```bash
# Run both and compare
scripts/hybrid_search.sh "best NAS for home use 2025"
```

## Configuration

### Perplexity
Create `config.json` with your API key:
```json
{
  "apiKey": "pplx-your-key-here"
}
```

### SearXNG
SearXNG defaults to `http://localhost:55510/search`. To use public instances:
```json
{
  "searxngUrl": "https://searx.be/search"
}
```

## Setup Requirements

### Perplexity
- Perplexity API key from https://perplexity.ai
- Free tier includes limited searches

### SearXNG (Optional - Local)
```bash
# Docker (recommended)
docker pull searxng/searxng
docker run -d -p 55510:8080 --rm -v ./searxng:/etc/searxng --name searxng searxng/searxng

# Or use public instance
```

## Models

### Perplexity Models
| Model | Use Case |
|-------|----------|
| sonar | Fast factual queries |
| sonar-pro | Better understanding |
| sonar-reasoning | Complex reasoning |
| sonar-research | Deep research |

### SearXNG
- Returns raw JSON with title, url, snippet, engine
- No model selection needed

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/straff2002) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
