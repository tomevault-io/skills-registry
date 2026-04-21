---
name: syntra-ai
description: AI features and vector search with Syntra. Use when configuring AI models, running chat completions, generating images, creating embeddings, building semantic search, setting up RAG pipelines, or managing vector collections. Use when this capability is needed.
metadata:
  author: mauricioperera
---

# Syntra AI

All AI features route through OpenRouter, supporting 200+ models from OpenAI, Anthropic, Google, Meta, Mistral, and more.

## Setup

1. Set `OPENROUTER_API_KEY` in environment
2. `ai_create_config` — define a model configuration
3. Use the config ID for inference calls

### Create a config

```json
{
  "input_modality": "text",
  "output_modality": "text",
  "provider": "openrouter",
  "model_id": "anthropic/claude-sonnet-4-5-20250929",
  "system_prompt": "You are a helpful assistant."
}
```

## AI model configs

- `ai_list_configs` — list all configs
- `ai_create_config` — create a new config (model, provider, modality, system prompt)
- `ai_get_config` / `ai_update_config` / `ai_delete_config` — CRUD operations

## Inference

### Chat completion

```json
{
  "config_id": "uuid-of-text-config",
  "messages": [
    { "role": "system", "content": "You are a coding assistant." },
    { "role": "user", "content": "Explain closures in JavaScript." }
  ],
  "temperature": 0.7,
  "max_tokens": 1000
}
```

### Image generation

```json
{
  "config_id": "uuid-of-image-config",
  "prompt": "A futuristic city at sunset, cyberpunk style",
  "n": 1,
  "size": "1024x1024"
}
```

### Embeddings

```json
{
  "config_id": "uuid-of-embedding-config",
  "input": "The quick brown fox jumps over the lazy dog"
}
```

Or batch: `"input": ["text one", "text two", "text three"]`

## Vector search (RAG)

Syntra includes built-in vector storage powered by pgvector.

### Workflow

1. `ai_create_collection` — create a vector collection
2. `ai_upsert_documents` — add documents (embeddings auto-generated)
3. `ai_vector_search` — semantic search by text query

### Collection management

- `ai_list_collections` / `ai_get_collection` / `ai_delete_collection`
- Each collection has: `name`, `embedding_model`, `dimensions`, `distance_metric`

### Distance metrics

| Metric | Use case |
|---|---|
| `cosine` | General-purpose similarity (default) |
| `l2` | Euclidean distance |
| `inner_product` | When vectors are normalized |

### Document operations

- `ai_upsert_documents` — insert or update documents with auto-embedding
- `ai_delete_documents` — remove by IDs
- `ai_vector_search` — search by text query with optional metadata filter
- `ai_vector_search_by_vector` — search by raw embedding vector

## Reference docs

- For the complete RAG workflow: see [vector-workflow.md](references/vector-workflow.md)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mauricioperera) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
