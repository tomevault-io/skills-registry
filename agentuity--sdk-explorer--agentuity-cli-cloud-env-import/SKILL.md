---
name: agentuity-cli-cloud-env-import
description: Import environment variables from a file to cloud and local .env. Requires authentication. Use for Agentuity cloud platform operations Use when this capability is needed.
metadata:
  author: agentuity
---

# Cloud Env Import

Import environment variables from a file to cloud and local .env

## Prerequisites

- Authenticated with `agentuity auth login`
- Project context required (run from project directory or use `--project-id`)

## Usage

```bash
agentuity cloud env import <file>
```

## Arguments

| Argument | Type | Required | Description |
|----------|------|----------|-------------|
| `<file>` | string | Yes | - |

## Examples

Import environment variables from .env file:

```bash
bunx @agentuity/cli cloud env import .env
```

Import from .env.local file:

```bash
bunx @agentuity/cli cloud env import .env.local
```

## Output

Returns JSON object:

```json
{
  "success": "boolean",
  "imported": "number",
  "skipped": "number",
  "path": "string",
  "file": "string"
}
```

| Field | Type | Description |
|-------|------|-------------|
| `success` | boolean | Whether import succeeded |
| `imported` | number | Number of items imported |
| `skipped` | number | Number of items skipped |
| `path` | string | Local file path where variables were saved |
| `file` | string | Source file path |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/agentuity) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
