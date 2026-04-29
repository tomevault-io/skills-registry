---
name: claude
description: Anthropic Claude integration. Chat with Claude models via Anthropic API. Use when this capability is needed.
metadata:
  author: sundial-org
---

# Claude 🧠

Anthropic Claude integration.

## Setup

```bash
export ANTHROPIC_API_KEY="sk-ant-..."
```

## Features

- Chat with Claude (Opus, Sonnet, Haiku)
- Long context support (200K tokens)
- Vision capabilities
- Tool use

## Usage Examples

```
"Ask Claude: Analyze this code"
"Use Claude to summarize this document"
```

## API Reference

```bash
curl -s https://api.anthropic.com/v1/messages \
  -H "x-api-key: $ANTHROPIC_API_KEY" \
  -H "anthropic-version: 2023-06-01" \
  -H "Content-Type: application/json" \
  -d '{"model":"claude-sonnet-4-20250514","max_tokens":1024,"messages":[{"role":"user","content":"Hello"}]}'
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sundial-org) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
