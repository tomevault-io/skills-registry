---
name: agentuity-cli-cloud-deployment-remove
description: Remove a specific deployment. Requires authentication. Use for Agentuity cloud platform operations Use when this capability is needed.
metadata:
  author: agentuity
---

# Cloud Deployment Remove

Remove a specific deployment

## Prerequisites

- Authenticated with `agentuity auth login`
- cloud deploy

## Usage

```bash
agentuity cloud deployment remove <deployment_id> [options]
```

## Arguments

| Argument | Type | Required | Description |
|----------|------|----------|-------------|
| `<deployment_id>` | string | Yes | - |

## Options

| Option | Type | Required | Default | Description |
|--------|------|----------|---------|-------------|
| `--project-id` | string | Yes | - | Project ID |
| `--force` | boolean | No | `false` | Force removal without confirmation |

## Examples

Remove with confirmation:

```bash
bunx @agentuity/cli cloud deployment remove dep_abc123xyz
```

Remove without confirmation:

```bash
bunx @agentuity/cli cloud deployment remove dep_abc123xyz --force
```

Remove deployment from specific project:

```bash
bunx @agentuity/cli cloud deployment remove deployment-2024-11-20 --project-id=proj_abc123xyz
```

## Output

Returns JSON object:

```json
{
  "success": "boolean",
  "projectId": "string",
  "deploymentId": "string"
}
```

| Field | Type | Description |
|-------|------|-------------|
| `success` | boolean | Whether the removal succeeded |
| `projectId` | string | Project ID |
| `deploymentId` | string | Deployment ID that was removed |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/agentuity) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
