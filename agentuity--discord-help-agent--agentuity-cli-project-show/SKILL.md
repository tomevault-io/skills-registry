---
name: agentuity-cli-project-show
description: Show project detail. Requires authentication. Use for project management operations Use when this capability is needed.
metadata:
  author: agentuity
---

# Project Show

Show project detail

## Prerequisites

- Authenticated with `agentuity auth login`

## Usage

```bash
agentuity project show <id>
```

## Arguments

| Argument | Type | Required | Description |
|----------|------|----------|-------------|
| `<id>` | string | Yes | - |

## Examples

Show details:

```bash
bunx @agentuity/cli project show proj_abc123def456
```

Show output in JSON format:

```bash
bunx @agentuity/cli --json project show proj_abc123def456
```

Get item details:

```bash
bunx @agentuity/cli project get proj_abc123def456
```

## Output

Returns JSON object:

```json
{
  "id": "string",
  "name": "string",
  "description": "unknown",
  "tags": "unknown",
  "orgId": "string",
  "secrets": "object",
  "env": "object"
}
```

| Field | Type | Description |
|-------|------|-------------|
| `id` | string | Project ID |
| `name` | string | Project name |
| `description` | unknown | Project description |
| `tags` | unknown | Project tags |
| `orgId` | string | Organization ID |
| `secrets` | object | Project secrets (masked) |
| `env` | object | Environment variables |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/agentuity) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
