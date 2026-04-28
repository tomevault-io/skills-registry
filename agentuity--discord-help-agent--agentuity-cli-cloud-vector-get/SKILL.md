---
name: agentuity-cli-cloud-vector-get
description: Get a specific vector entry by key. Requires authentication. Use for Agentuity cloud platform operations Use when this capability is needed.
metadata:
  author: agentuity
---

# Cloud Vector Get

Get a specific vector entry by key

## Prerequisites

- Authenticated with `agentuity auth login`
- Project context required (run from project directory or use `--project-id`)

## Usage

```bash
agentuity cloud vector get <namespace> <key>
```

## Arguments

| Argument | Type | Required | Description |
|----------|------|----------|-------------|
| `<namespace>` | string | Yes | - |
| `<key>` | string | Yes | - |

## Examples

Get a specific product vector:

```bash
bunx @agentuity/cli vector get products chair-001
```

Get a document from knowledge base:

```bash
bunx @agentuity/cli vector get knowledge-base doc-123
```

Get user profile embedding:

```bash
bunx @agentuity/cli vector get embeddings user-profile-456
```

## Output

Returns JSON object:

```json
{
  "exists": "boolean",
  "key": "string",
  "id": "string",
  "metadata": "object",
  "document": "string",
  "similarity": "number"
}
```

| Field | Type | Description |
|-------|------|-------------|
| `exists` | boolean | Whether the vector exists |
| `key` | string | Vector key |
| `id` | string | Vector ID |
| `metadata` | object | Vector metadata |
| `document` | string | Original document text |
| `similarity` | number | Similarity score |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/agentuity) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
