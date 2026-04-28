---
name: agentuity-cli-cloud-keyvalue-stats
description: Get statistics for keyvalue storage. Requires authentication. Use for Agentuity cloud platform operations Use when this capability is needed.
metadata:
  author: agentuity
---

# Cloud Keyvalue Stats

Get statistics for keyvalue storage

## Prerequisites

- Authenticated with `agentuity auth login`
- Project context required (run from project directory or use `--project-id`)

## Usage

```bash
agentuity cloud keyvalue stats [name]
```

## Arguments

| Argument | Type | Required | Description |
|----------|------|----------|-------------|
| `<name>` | string | No | - |

## Examples

Show stats for all namespaces:

```bash
bunx @agentuity/cli kv stats
```

Show stats for production namespace:

```bash
bunx @agentuity/cli kv stats production
```

Show stats for cache namespace:

```bash
bunx @agentuity/cli kv stats cache
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/agentuity) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
