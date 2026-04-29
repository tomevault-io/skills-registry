---
name: agentuity-cli-cloud-machine-deployments
description: List deployments running on a specific organization managed machine. Requires authentication. Use for Agentuity cloud platform operations Use when this capability is needed.
metadata:
  author: agentuity
---

# Cloud Machine Deployments

List deployments running on a specific organization managed machine

## Prerequisites

- Authenticated with `agentuity auth login`
- Organization context required (`--org-id` or default org)

## Usage

```bash
agentuity cloud machine deployments <machine_id>
```

## Arguments

| Argument | Type | Required | Description |
|----------|------|----------|-------------|
| `<machine_id>` | string | Yes | - |

## Examples

List deployments on a machine:

```bash
bunx @agentuity/cli cloud machine deployments machine_abc123xyz
```

## Output

Returns: `array`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/agentuity) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-12 -->
