---
name: agentuity-cli-git-list
description: List GitHub repositories accessible to your organization. Requires authentication Use when this capability is needed.
metadata:
  author: agentuity
---

# Git List

List GitHub repositories accessible to your organization

## Prerequisites

- Authenticated with `agentuity auth login`

## Usage

```bash
agentuity git list [options]
```

## Options

| Option | Type | Required | Default | Description |
|--------|------|----------|---------|-------------|
| `--org` | string | Yes | - | Organization ID to list repos for |
| `--account` | string | Yes | - | GitHub account/integration ID to filter by |

## Examples

List all accessible GitHub repositories:

```bash
bunx @agentuity/cli git list
```

List repos for a specific organization:

```bash
bunx @agentuity/cli git list --org org_abc123
```

List repos in JSON format:

```bash
bunx @agentuity/cli --json git list
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/agentuity) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
