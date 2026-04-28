---
name: agentuity-cli-profile-delete
description: Delete a configuration profile Use when this capability is needed.
metadata:
  author: agentuity
---

# Profile Delete

Delete a configuration profile

## Usage

```bash
agentuity profile delete [name] [options]
```

## Arguments

| Argument | Type | Required | Description |
|----------|------|----------|-------------|
| `<name>` | string | No | - |

## Options

| Option | Type | Required | Default | Description |
|--------|------|----------|---------|-------------|
| `--confirm` | boolean | Yes | - | Skip confirmation prompt |

## Examples

Delete item:

```bash
bunx @agentuity/cli profile delete staging
```

Use confirm option:

```bash
bunx @agentuity/cli profile delete old-dev --confirm
```

Delete item:

```bash
bunx @agentuity/cli profile delete
```

## Output

Returns JSON object:

```json
{
  "success": "boolean",
  "name": "string"
}
```

| Field | Type | Description |
|-------|------|-------------|
| `success` | boolean | Whether deletion succeeded |
| `name` | string | Deleted profile name |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/agentuity) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
