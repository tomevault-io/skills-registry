---
name: agentuity-cli-cloud-env-list
description: List all environment variables. Requires authentication. Use for Agentuity cloud platform operations Use when this capability is needed.
metadata:
  author: agentuity
---

# Cloud Env List

List all environment variables

## Prerequisites

- Authenticated with `agentuity auth login`
- Project context required (run from project directory or use `--project-id`)

## Usage

```bash
agentuity cloud env list [options]
```

## Options

| Option | Type | Required | Default | Description |
|--------|------|----------|---------|-------------|
| `--mask` | boolean | No | `false` | mask the values in output (default: false for env vars) |

## Examples

List items:

```bash
bunx @agentuity/cli env list
```

Use mask option:

```bash
bunx @agentuity/cli env list --mask
```

## Output

Returns: `object`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/agentuity) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
