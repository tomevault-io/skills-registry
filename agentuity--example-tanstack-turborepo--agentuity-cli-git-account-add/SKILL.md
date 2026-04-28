---
name: agentuity-cli-git-account-add
description: Add a GitHub account to your organization. Requires authentication Use when this capability is needed.
metadata:
  author: agentuity
---

# Git Account Add

Add a GitHub account to your organization

## Prerequisites

- Authenticated with `agentuity auth login`

## Usage

```bash
agentuity git account add [options]
```

## Options

| Option | Type | Required | Default | Description |
|--------|------|----------|---------|-------------|
| `--org` | string | Yes | - | Organization ID to add the account to |

## Examples

Add a GitHub account to your organization:

```bash
bunx @agentuity/cli git account add
```

Add to a specific organization:

```bash
bunx @agentuity/cli git account add --org org_abc123
```

## Output

Returns JSON object:

```json
{
  "connected": "boolean",
  "orgId": "string"
}
```

| Field | Type | Description |
|-------|------|-------------|
| `connected` | boolean | Whether the account was connected |
| `orgId` | string | Organization ID |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/agentuity) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
