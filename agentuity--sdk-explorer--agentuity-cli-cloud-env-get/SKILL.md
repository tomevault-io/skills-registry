---
name: agentuity-cli-cloud-env-get
description: Get an environment variable value. Requires authentication. Use for Agentuity cloud platform operations Use when this capability is needed.
metadata:
  author: agentuity
---

# Cloud Env Get

Get an environment variable value

## Prerequisites

- Authenticated with `agentuity auth login`
- Project context required (run from project directory or use `--project-id`)

## Usage

```bash
agentuity cloud env get <key> [options]
```

## Arguments

| Argument | Type | Required | Description |
|----------|------|----------|-------------|
| `<key>` | string | Yes | - |

## Options

| Option | Type | Required | Default | Description |
|--------|------|----------|---------|-------------|
| `--mask` | boolean | No | `false` | mask the value in output (default: true in TTY, false otherwise) |

## Examples

Get item details:

```bash
bunx @agentuity/cli env get NODE_ENV
```

Get item details:

```bash
bunx @agentuity/cli env get LOG_LEVEL
```

## Output

Returns JSON object:

```json
{
  "key": "string",
  "value": "string"
}
```

| Field | Type | Description |
|-------|------|-------------|
| `key` | string | Environment variable key name |
| `value` | string | Environment variable value |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/agentuity) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
