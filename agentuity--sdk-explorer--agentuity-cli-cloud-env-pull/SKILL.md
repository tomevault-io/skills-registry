---
name: agentuity-cli-cloud-env-pull
description: Pull environment variables from cloud to local .env file. Requires authentication. Use for Agentuity cloud platform operations Use when this capability is needed.
metadata:
  author: agentuity
---

# Cloud Env Pull

Pull environment variables from cloud to local .env file

## Prerequisites

- Authenticated with `agentuity auth login`
- Project context required (run from project directory or use `--project-id`)
- cloud deploy

## Usage

```bash
agentuity cloud env pull [options]
```

## Options

| Option | Type | Required | Default | Description |
|--------|------|----------|---------|-------------|
| `--force` | boolean | No | `false` | overwrite local values with cloud values |

## Examples

Run pull command:

```bash
bunx @agentuity/cli env pull
```

Use force option:

```bash
bunx @agentuity/cli env pull --force
```

## Output

Returns JSON object:

```json
{
  "success": "boolean",
  "pulled": "number",
  "path": "string",
  "force": "boolean"
}
```

| Field | Type | Description |
|-------|------|-------------|
| `success` | boolean | Whether pull succeeded |
| `pulled` | number | Number of items pulled |
| `path` | string | Local file path where variables were saved |
| `force` | boolean | Whether force mode was used |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/agentuity) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
