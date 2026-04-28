---
name: agentuity-cli-cloud-secret-push
description: Push secrets from local .env file to cloud. Requires authentication. Use for Agentuity cloud platform operations Use when this capability is needed.
metadata:
  author: agentuity
---

# Cloud Secret Push

Push secrets from local .env file to cloud

## Prerequisites

- Authenticated with `agentuity auth login`
- Project context required (run from project directory or use `--project-id`)
- secret set

## Usage

```bash
agentuity cloud secret push
```

## Examples

Run push command:

```bash
bunx @agentuity/cli secret push
```

## Output

Returns JSON object:

```json
{
  "success": "boolean",
  "pushed": "number",
  "source": "string"
}
```

| Field | Type | Description |
|-------|------|-------------|
| `success` | boolean | Whether push succeeded |
| `pushed` | number | Number of items pushed |
| `source` | string | Source file path |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/agentuity) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
