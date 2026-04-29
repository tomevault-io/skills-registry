---
name: agentuity-cli-cloud-region-select
description: Set the default cloud region for all commands. Requires authentication. Use for Agentuity cloud platform operations Use when this capability is needed.
metadata:
  author: agentuity
---

# Cloud Region Select

Set the default cloud region for all commands

## Prerequisites

- Authenticated with `agentuity auth login`

## Usage

```bash
agentuity cloud region select [region]
```

## Arguments

| Argument | Type | Required | Description |
|----------|------|----------|-------------|
| `<region>` | string | No | - |

## Examples

Select default region:

```bash
bunx @agentuity/cli cloud region select
```

Set specific region as default:

```bash
bunx @agentuity/cli cloud region select usc
```

## Output

Returns JSON object:

```json
{
  "region": "string",
  "description": "string"
}
```

| Field | Type | Description |
|-------|------|-------------|
| `region` | string | The selected region code |
| `description` | string | The region description |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/agentuity) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-12 -->
