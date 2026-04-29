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
- env set

## Usage

```bash
agentuity cloud env push [options]
```

## Options

| Option | Type | Required | Default | Description |
|--------|------|----------|---------|-------------|
| `--org` | optionalString | Yes | - | push to organization level (use --org for default org) |

## Examples

Push all variables to cloud (project):

```bash
bunx @agentuity/cli env push
```

Push all variables to organization:

```bash
bunx @agentuity/cli env push --org
```

## Output

Returns JSON object:

```json
{
  "success": "boolean",
  "pushed": "number",
  "envCount": "number",
  "secretCount": "number",
  "source": "string",
  "scope": "string"
}
```

| Field | Type | Description |
|-------|------|-------------|
| `success` | boolean | Whether push succeeded |
| `pushed` | number | Number of items pushed |
| `envCount` | number | Number of env vars pushed |
| `secretCount` | number | Number of secrets pushed |
| `source` | string | Source file path |
| `scope` | string | The scope where variables were pushed |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/agentuity) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-12 -->
