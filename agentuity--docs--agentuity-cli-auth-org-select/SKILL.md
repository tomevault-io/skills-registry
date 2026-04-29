---
name: agentuity-cli-auth-org-select
description: Set the default organization for all commands. Requires authentication. Use for managing authentication credentials Use when this capability is needed.
metadata:
  author: agentuity
---

# Auth Org Select

Set the default organization for all commands

## Prerequisites

- Authenticated with `agentuity auth login`

## Usage

```bash
agentuity auth org select [org_id]
```

## Arguments

| Argument | Type | Required | Description |
|----------|------|----------|-------------|
| `<org_id>` | string | No | - |

## Examples

Select default organization:

```bash
bunx @agentuity/cli auth org select
```

Set specific organization as default:

```bash
bunx @agentuity/cli auth org select org_abc123
```

## Output

Returns JSON object:

```json
{
  "orgId": "string",
  "name": "string"
}
```

| Field | Type | Description |
|-------|------|-------------|
| `orgId` | string | The selected organization ID |
| `name` | string | The organization name |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/agentuity) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-12 -->
