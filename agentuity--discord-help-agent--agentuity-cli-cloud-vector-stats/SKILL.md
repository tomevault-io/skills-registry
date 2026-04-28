---
name: agentuity-cli-cloud-vector-stats
description: Get statistics for vector storage. Requires authentication. Use for Agentuity cloud platform operations Use when this capability is needed.
metadata:
  author: agentuity
---

# Cloud Vector Stats

Get statistics for vector storage

## Prerequisites

- Authenticated with `agentuity auth login`
- Project context required (run from project directory or use `--project-id`)

## Usage

```bash
agentuity cloud vector stats [name]
```

## Arguments

| Argument | Type | Required | Description |
|----------|------|----------|-------------|
| `<name>` | string | No | - |

## Examples

Show stats for all namespaces:

```bash
bunx @agentuity/cli vector stats
```

Show detailed stats for products namespace:

```bash
bunx @agentuity/cli vector stats products
```

Show stats for embeddings:

```bash
bunx @agentuity/cli vector stats embeddings
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/agentuity) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
