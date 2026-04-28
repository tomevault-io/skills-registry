---
name: agentuity-cli-cloud-env-push
description: Push environment variables and secrets from local .env file to cloud. Requires authentication. Use for Agentuity cloud platform operations Use when this capability is needed.
metadata:
  author: agentuity
---

# Cloud Env Push

Push environment variables and secrets from local .env file to cloud

## Prerequisites

- Authenticated with `agentuity auth login`
- Project context required (run from project directory or use `--project-id`)
- env set

## Usage

```bash
agentuity cloud env push
```

## Examples

Push all variables to cloud:

```bash
bunx @agentuity/cli env push
```

## Output

Returns JSON object:

```json
{
  "success": "boolean",
  "pushed": "number",
  "envCount": "number",
  "secretCount": "number",
  "source": "string"
}
```

| Field | Type | Description |
|-------|------|-------------|
| `success` | boolean | Whether push succeeded |
| `pushed` | number | Number of items pushed |
| `envCount` | number | Number of env vars pushed |
| `secretCount` | number | Number of secrets pushed |
| `source` | string | Source file path |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/agentuity) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
