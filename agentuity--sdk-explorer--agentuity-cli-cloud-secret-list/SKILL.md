---
name: agentuity-cli-cloud-secret-list
description: List all secrets. Requires authentication. Use for Agentuity cloud platform operations Use when this capability is needed.
metadata:
  author: agentuity
---

# Cloud Secret List

List all secrets

## Prerequisites

- Authenticated with `agentuity auth login`
- Project context required (run from project directory or use `--project-id`)

## Usage

```bash
agentuity cloud secret list [options]
```

## Options

| Option | Type | Required | Default | Description |
|--------|------|----------|---------|-------------|
| `--mask` | boolean | No | `true` | mask the values in output (default: true in TTY for secrets) |

## Examples

List items:

```bash
bunx @agentuity/cli secret list
```

Use no mask option:

```bash
bunx @agentuity/cli secret list --no-mask
```

## Output

Returns: `object`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/agentuity) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
