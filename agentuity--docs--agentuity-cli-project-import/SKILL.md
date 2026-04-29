---
name: agentuity-cli-project-import
description: Import or register a local project with Agentuity Cloud. Requires authentication. Use for project management operations Use when this capability is needed.
metadata:
  author: agentuity
---

# Project Import

Import or register a local project with Agentuity Cloud

## Prerequisites

- Authenticated with `agentuity auth login`

## Usage

```bash
agentuity project import [options]
```

## Options

| Option | Type | Required | Default | Description |
|--------|------|----------|---------|-------------|
| `--dir` | string | Yes | - | Directory containing the project (default: current directory) |
| `--validateOnly` | boolean | Yes | - | Only validate the project structure without prompting |

## Examples

Import project in current directory:

```bash
bunx @agentuity/cli project import
```

Import project from specified directory:

```bash
bunx @agentuity/cli project import --dir ./my-agent
```

## Output

Returns JSON object:

```json
{
  "success": "boolean",
  "projectId": "string",
  "orgId": "string",
  "region": "string",
  "status": "string",
  "message": "string"
}
```

| Field | Type | Description |
|-------|------|-------------|
| `success` | boolean | Whether the import succeeded |
| `projectId` | string | Project ID if imported |
| `orgId` | string | Organization ID |
| `region` | string | Region |
| `status` | string | The result status of the import |
| `message` | string | Status message |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/agentuity) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-12 -->
