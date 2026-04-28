---
name: agentuity-cli-cloud-secret-import
description: Import secrets from a file to cloud and local .env. Requires authentication. Use for Agentuity cloud platform operations Use when this capability is needed.
metadata:
  author: agentuity
---

# Cloud Secret Import

Import secrets from a file to cloud and local .env

## Prerequisites

- Authenticated with `agentuity auth login`
- Project context required (run from project directory or use `--project-id`)

## Usage

```bash
agentuity cloud secret import <file>
```

## Arguments

| Argument | Type | Required | Description |
|----------|------|----------|-------------|
| `<file>` | string | Yes | - |

## Examples

Run .env.local command:

```bash
bunx @agentuity/cli secret import .env.local
```

Run .env.backup command:

```bash
bunx @agentuity/cli secret import .env.backup
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
| `path` | string | Local file path where secrets were saved |
| `file` | string | Source file path |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/agentuity) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
