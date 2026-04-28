---
name: agentuity-cli-cloud-env-get
description: Get an environment variable or secret value. Requires authentication. Use for Agentuity cloud platform operations Use when this capability is needed.
metadata:
  author: agentuity
---

# Cloud Env Get

Get an environment variable or secret value

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
| `--maskSecret` | boolean | Yes | - | mask the secret value in output |

## Examples

Get environment variable:

```bash
bunx @agentuity/cli env get NODE_ENV
```

Get a secret value:

```bash
bunx @agentuity/cli env get API_KEY
```

Show unmasked value:

```bash
bunx @agentuity/cli env get API_KEY --no-mask
```

## Output

Returns JSON object:

```json
{
  "key": "string",
  "value": "string",
  "secret": "boolean"
}
```

| Field | Type | Description |
|-------|------|-------------|
| `key` | string | Environment variable key name |
| `value` | string | Environment variable value |
| `secret` | boolean | Whether the value is stored as a secret |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/agentuity) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
