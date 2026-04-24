---
name: libllm
description: > Use when this capability is needed.
metadata:
  author: copilot-ld
---

# libllm Skill

## When to Use

- Making chat completion requests to LLM providers
- Generating text embeddings for vector search
- Integrating with OpenAI-compatible APIs
- Handling streaming LLM responses

## Key Concepts

**LlmApi**: HTTP client for OpenAI-compatible endpoints. Handles authentication,
streaming, and response parsing.

**DEFAULT_MAX_TOKENS**: Standard token limit for completions.

## Usage Patterns

### Pattern 1: Chat completion

```javascript
import { LlmApi } from "@copilot-ld/libllm";

const api = new LlmApi(config, logger);
const response = await api.completion([{ role: "user", content: "Hello" }], {
  model: "gpt-4",
  maxTokens: 1000,
});
```

### Pattern 2: Generate embeddings

```javascript
const embeddings = await api.embed(["text to embed"]);
// Returns array of vectors
```

## Integration

Used by LLM service. Configurable via environment for different providers
(OpenAI, Azure, GitHub Models).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/copilot-ld) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
