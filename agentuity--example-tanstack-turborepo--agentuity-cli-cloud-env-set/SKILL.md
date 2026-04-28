---
name: agentuity-cli-cloud-env-set
description: Set an environment variable or secret. Requires authentication. Use for Agentuity cloud platform operations Use when this capability is needed.
metadata:
  author: agentuity
---

# Cloud Env Set

Set an environment variable or secret

## Prerequisites

- Authenticated with `agentuity auth login`
- Project context required (run from project directory or use `--project-id`)

## Usage

```bash
agentuity cloud env set <key> <value> [options]
```

## Arguments

| Argument | Type | Required | Description |
|----------|------|----------|-------------|
| `<key>` | string | Yes | - |
| `<value>` | string | Yes | - |

## Options

| Option | Type | Required | Default | Description |
|--------|------|----------|---------|-------------|
| `--secret` | boolean | No | `false` | store as a secret (encrypted and masked in UI) |

## Examples

Set environment variable:

```bash
bunx @agentuity/cli env set NODE_ENV production
```

Set port number:

```bash
bunx @agentuity/cli env set PORT 3000
```

Set a secret value:

```bash
bunx @agentuity/cli env set API_KEY "sk_..." --secret
```

## Output

Returns JSON object:

```json
{
  "success": "boolean",
  "key": "string",
  "path": "string",
  "secret": "boolean"
}
```

| Field | Type | Description |
|-------|------|-------------|
| `success` | boolean | Whether the operation succeeded |
| `key` | string | Environment variable key |
| `path` | string | Local file path where env var was saved |
| `secret` | boolean | Whether the value was stored as a secret |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/agentuity) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
