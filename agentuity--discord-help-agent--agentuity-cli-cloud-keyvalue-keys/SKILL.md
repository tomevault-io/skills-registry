---
name: agentuity-cli-cloud-keyvalue-keys
description: List all keys in a keyvalue namespace. Requires authentication. Use for Agentuity cloud platform operations Use when this capability is needed.
metadata:
  author: agentuity
---

# Cloud Keyvalue Keys

List all keys in a keyvalue namespace

## Prerequisites

- Authenticated with `agentuity auth login`
- Project context required (run from project directory or use `--project-id`)

## Usage

```bash
agentuity cloud keyvalue keys <name>
```

## Arguments

| Argument | Type | Required | Description |
|----------|------|----------|-------------|
| `<name>` | string | Yes | - |

## Examples

List all keys in production:

```bash
bunx @agentuity/cli kv keys production
```

List all cached keys (using alias):

```bash
bunx @agentuity/cli kv ls cache
```

List all staging keys:

```bash
bunx @agentuity/cli kv list staging
```

## Output

Returns JSON object:

```json
{
  "namespace": "string",
  "keys": "array"
}
```

| Field | Type | Description |
|-------|------|-------------|
| `namespace` | string | Namespace name |
| `keys` | array | List of keys in the namespace |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/agentuity) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
