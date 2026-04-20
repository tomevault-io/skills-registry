---
name: perplexity
description: Search the web with AI-powered answers and citations via Perplexity API. Use for research queries needing current info with sources. Env: PERPLEXITY_API_KEY. Use when this capability is needed.
metadata:
  author: dpaluy
---

# Perplexity Search

Web search with AI synthesis and citations.

## Setup

```bash
export PERPLEXITY_API_KEY=pplx-...  # Get at perplexity.ai/settings/api
```

## Search Query

```bash
curl -s --request POST \
  --url https://api.perplexity.ai/chat/completions \
  --header "Authorization: Bearer $PERPLEXITY_API_KEY" \
  --header "Content-Type: application/json" \
  --data '{
    "model": "sonar-pro",
    "messages": [{"role": "user", "content": "What are the latest developments in quantum computing?"}]
  }' | jq -r '.choices[0].message.content'
```

## Models

| Model | Use Case |
|-------|----------|
| `sonar` | Fast, simple queries |
| `sonar-pro` | Complex questions (default) |
| `sonar-deep-research` | Thorough research |
| `sonar-reasoning-pro` | Multi-step reasoning |

## Streaming

```bash
curl --request POST \
  --url https://api.perplexity.ai/chat/completions \
  --header "Authorization: Bearer $PERPLEXITY_API_KEY" \
  --header "Content-Type: application/json" \
  --data '{
    "model": "sonar-pro",
    "messages": [{"role": "user", "content": "Explain machine learning"}],
    "stream": true
  }'
```

## Raw Search API

For web results without AI synthesis ($5/1000 requests, no token costs):

```bash
curl -s --request POST \
  --url https://api.perplexity.ai/search \
  --header "Authorization: Bearer $PERPLEXITY_API_KEY" \
  --header "Content-Type: application/json" \
  --data '{
    "query": "latest AI developments",
    "max_results": 5,
    "max_tokens_per_page": 2048
  }'
```

## Response Parsing

```bash
# Get answer
| jq -r '.choices[0].message.content'

# Get citations
| jq -r '.citations[]'

# Get usage stats
| jq '.usage'
```

## Errors

- `401` — Invalid API key
- `429` — Rate limited (wait/retry)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/dpaluy) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
