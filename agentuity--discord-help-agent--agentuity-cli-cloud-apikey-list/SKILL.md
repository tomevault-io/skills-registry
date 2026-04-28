---
name: agentuity-cli-cloud-apikey-list
description: List all API keys. Requires authentication. Use for Agentuity cloud platform operations Use when this capability is needed.
metadata:
  author: agentuity
---

# Cloud Apikey List

List all API keys

## Prerequisites

- Authenticated with `agentuity auth login`

## Usage

```bash
agentuity cloud apikey list [options]
```

## Options

| Option | Type | Required | Default | Description |
|--------|------|----------|---------|-------------|
| `--orgId` | string | Yes | - | filter by organization id |
| `--projectId` | string | Yes | - | filter by project id |

## Examples

List items:

```bash
bunx @agentuity/cli cloud apikey list
```

List items:

```bash
bunx @agentuity/cli cloud apikey ls
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/agentuity) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
