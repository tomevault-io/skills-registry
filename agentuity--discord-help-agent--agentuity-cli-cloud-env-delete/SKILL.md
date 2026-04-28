---
name: agentuity-cli-cloud-env-delete
description: Delete an environment variable. Requires authentication. Use for Agentuity cloud platform operations Use when this capability is needed.
metadata:
  author: agentuity
---

# Cloud Env Delete

Delete an environment variable

## Prerequisites

- Authenticated with `agentuity auth login`
- Project context required (run from project directory or use `--project-id`)

## Usage

```bash
agentuity cloud env delete <key>
```

## Arguments

| Argument | Type | Required | Description |
|----------|------|----------|-------------|
| `<key>` | string | Yes | - |

## Examples

Delete item:

```bash
bunx @agentuity/cli env delete OLD_FEATURE_FLAG
```

Delete item:

```bash
bunx @agentuity/cli env rm PORT
```

## Output

Returns JSON object:

```json
{
  "success": "boolean",
  "key": "string",
  "path": "string"
}
```

| Field | Type | Description |
|-------|------|-------------|
| `success` | boolean | Whether the operation succeeded |
| `key` | string | Environment variable key that was deleted |
| `path` | string | Local file path where env var was removed |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/agentuity) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
