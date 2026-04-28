---
name: agentuity-cli-cloud-deployment-list
description: List deployments. Requires authentication. Use for Agentuity cloud platform operations Use when this capability is needed.
metadata:
  author: agentuity
---

# Cloud Deployment List

List deployments

## Prerequisites

- Authenticated with `agentuity auth login`

## Usage

```bash
agentuity cloud deployment list [options]
```

## Options

| Option | Type | Required | Default | Description |
|--------|------|----------|---------|-------------|
| `--project-id` | string | Yes | - | Project ID |
| `--count` | number | No | `10` | Number of deployments to list (1–100) |

## Examples

List 10 most recent deployments:

```bash
bunx @agentuity/cli cloud deployment list
```

List 25 most recent deployments:

```bash
bunx @agentuity/cli cloud deployment list --count=25
```

List deployments for specific project:

```bash
bunx @agentuity/cli cloud deployment list --project-id=proj_abc123xyz
```

## Output

Returns: `array`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/agentuity) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
