---
name: agentuity-cli-auth-org-unselect
description: Clear the default organization preference. Use for managing authentication credentials Use when this capability is needed.
metadata:
  author: agentuity
---

# Auth Org Unselect

Clear the default organization preference

## Usage

```bash
agentuity auth org unselect
```

## Examples

Clear default organization:

```bash
bunx @agentuity/cli auth org unselect
```

## Output

Returns JSON object:

```json
{
  "cleared": "boolean"
}
```

| Field | Type | Description |
|-------|------|-------------|
| `cleared` | boolean | Whether the preference was cleared |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/agentuity) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-12 -->
