---
name: gpt
description: OpenAI GPT integration. Chat completions, image generation, embeddings, and fine-tuning via OpenAI API. Use when this capability is needed.
metadata:
  author: sundial-org
---

# GPT 🤖

OpenAI GPT integration.

## Setup

```bash
export OPENAI_API_KEY="sk-..."
```

## Features

- Chat completions (GPT-4, GPT-4o)
- Image generation (DALL-E)
- Text embeddings
- Fine-tuning
- Assistants API

## Usage Examples

```
"Ask GPT: Explain quantum computing"
"Generate image of a sunset"
"Create embeddings for this text"
```

## API Reference

```bash
curl -s https://api.openai.com/v1/chat/completions \
  -H "Authorization: Bearer $OPENAI_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"model":"gpt-4o","messages":[{"role":"user","content":"Hello"}]}'
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sundial-org) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
