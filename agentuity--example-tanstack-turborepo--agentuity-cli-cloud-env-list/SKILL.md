---
name: agentuity-cli-cloud-env-list
description: List all environment variables and secrets. Requires authentication. Use for Agentuity cloud platform operations Use when this capability is needed.
metadata:
  author: agentuity
---

# Cloud Env List

List all environment variables and secrets

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
| `--mask` | boolean | No | `true` | mask secret values in output (use --no-mask to show values) |
| `--secrets` | boolean | No | `false` | list only secrets |
| `--env-only` | boolean | No | `false` | list only environment variables |

## Examples

List all variables:

```bash
bunx @agentuity/cli env list
```

Show unmasked values:

```bash
bunx @agentuity/cli env list --no-mask
```

List only secrets:

```bash
bunx @agentuity/cli env list --secrets
```

List only env vars:

```bash
bunx @agentuity/cli env list --env-only
```

## Output

Returns: `object`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/agentuity) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
