---
name: agentuity-cli-cloud-deployment-undeploy
description: Undeploy the latest deployment. Requires authentication. Use for Agentuity cloud platform operations Use when this capability is needed.
metadata:
  author: agentuity
---

# Cloud Deployment Undeploy

Undeploy the latest deployment

## Prerequisites

- Authenticated with `agentuity auth login`
- cloud deploy

## Usage

```bash
agentuity cloud deployment undeploy [options]
```

## Options

| Option | Type | Required | Default | Description |
|--------|------|----------|---------|-------------|
| `--project-id` | string | Yes | - | Project ID |
| `--force` | boolean | No | `false` | Force undeploy without confirmation |

## Examples

Undeploy with confirmation:

```bash
bunx @agentuity/cli cloud deployment undeploy
```

Undeploy without confirmation:

```bash
bunx @agentuity/cli cloud deployment undeploy --force
```

Undeploy specific project:

```bash
bunx @agentuity/cli cloud deployment undeploy --project-id=proj_abc123xyz
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/agentuity) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
