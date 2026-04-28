---
name: agentuity-cli-cloud-deployment-rollback
description: Rollback the latest to the previous deployment. Requires authentication. Use for Agentuity cloud platform operations Use when this capability is needed.
metadata:
  author: agentuity
---

# Cloud Deployment Rollback

Rollback the latest to the previous deployment

## Prerequisites

- Authenticated with `agentuity auth login`
- cloud deploy

## Usage

```bash
agentuity cloud deployment rollback [options]
```

## Options

| Option | Type | Required | Default | Description |
|--------|------|----------|---------|-------------|
| `--project-id` | string | Yes | - | Project ID |

## Examples

Rollback to previous deployment:

```bash
bunx @agentuity/cli cloud deployment rollback
```

Rollback specific project:

```bash
bunx @agentuity/cli cloud deployment rollback --project-id=proj_abc123xyz
```

## Output

Returns JSON object:

```json
{
  "success": "boolean",
  "projectId": "string",
  "targetDeploymentId": "string"
}
```

| Field | Type | Description |
|-------|------|-------------|
| `success` | boolean | Whether the rollback succeeded |
| `projectId` | string | Project ID |
| `targetDeploymentId` | string | Deployment ID that was rolled back to |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/agentuity) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
