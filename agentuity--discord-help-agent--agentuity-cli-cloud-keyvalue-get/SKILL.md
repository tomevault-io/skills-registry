---
name: agentuity-cli-cloud-keyvalue-get
description: Get a value from the keyvalue storage. Requires authentication. Use for Agentuity cloud platform operations Use when this capability is needed.
metadata:
  author: agentuity
---

# Cloud Keyvalue Get

Get a value from the keyvalue storage

## Prerequisites

- Authenticated with `agentuity auth login`
- Project context required (run from project directory or use `--project-id`)

## Usage

```bash
agentuity cloud keyvalue get <namespace> <key>
```

## Arguments

| Argument | Type | Required | Description |
|----------|------|----------|-------------|
| `<namespace>` | string | Yes | - |
| `<key>` | string | Yes | - |

## Examples

Get user data:

```bash
bunx @agentuity/cli kv get production user:123
```

Get cached session:

```bash
bunx @agentuity/cli kv get cache session:abc
```

Get homepage cache:

```bash
bunx @agentuity/cli kv get staging cache:homepage
```

## Output

Returns JSON object:

```json
{
  "exists": "boolean",
  "data": "unknown",
  "contentType": "string"
}
```

| Field | Type | Description |
|-------|------|-------------|
| `exists` | boolean | Whether the key exists |
| `data` | unknown | Value data (string or binary) |
| `contentType` | string | Content type |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/agentuity) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
