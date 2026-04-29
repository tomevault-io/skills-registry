---
name: agentuity-cli-cloud-region-unselect
description: Clear the default region preference. Use for Agentuity cloud platform operations Use when this capability is needed.
metadata:
  author: agentuity
---

# Cloud Region Unselect

Clear the default region preference

## Usage

```bash
agentuity cloud region unselect
```

## Examples

Clear default region:

```bash
bunx @agentuity/cli cloud region unselect
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
