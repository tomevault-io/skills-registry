---
name: agentuity-cli-cloud-apikey-get
description: Get a specific API key by id. Requires authentication. Use for Agentuity cloud platform operations Use when this capability is needed.
metadata:
  author: agentuity
---

# Cloud Apikey Get

Get a specific API key by id

## Prerequisites

- Authenticated with `agentuity auth login`

## Usage

```bash
agentuity cloud apikey get <id>
```

## Arguments

| Argument | Type | Required | Description |
|----------|------|----------|-------------|
| `<id>` | string | Yes | - |

## Examples

Get item details:

```bash
bunx @agentuity/cli cloud apikey get <id>
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/agentuity) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
