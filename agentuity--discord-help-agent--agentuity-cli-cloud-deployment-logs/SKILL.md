---
name: agentuity-cli-cloud-deployment-logs
description: View logs for a specific deployment. Requires authentication. Use for Agentuity cloud platform operations Use when this capability is needed.
metadata:
  author: agentuity
---

# Cloud Deployment Logs

View logs for a specific deployment

## Prerequisites

- Authenticated with `agentuity auth login`
- cloud deploy

## Usage

```bash
agentuity cloud deployment logs <deployment_id> [options]
```

## Arguments

| Argument | Type | Required | Description |
|----------|------|----------|-------------|
| `<deployment_id>` | string | Yes | - |

## Options

| Option | Type | Required | Default | Description |
|--------|------|----------|---------|-------------|
| `--projectId` | string | Yes | - | Project ID |
| `--limit` | number | No | `100` | Maximum number of logs to return |
| `--timestamps` | boolean | No | `true` | Show timestamps in output |

## Examples

View logs for deployment:

```bash
bunx @agentuity/cli cloud deployment logs deploy_abc123xyz
```

Limit to 50 log entries:

```bash
bunx @agentuity/cli cloud deployment logs deploy_abc123xyz --limit=50
```

Hide timestamps:

```bash
bunx @agentuity/cli cloud deployment logs deploy_abc123xyz --no-timestamps
```

View logs with specific project:

```bash
bunx @agentuity/cli cloud deployment logs deploy_abc123xyz --project-id=proj_abc123xyz
```

## Output

Returns: `array`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/agentuity) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
