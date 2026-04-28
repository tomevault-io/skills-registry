---
name: agentuity-cli-profile-create
description: Create a new configuration profile Use when this capability is needed.
metadata:
  author: agentuity
---

# Profile Create

Create a new configuration profile

## Usage

```bash
agentuity profile create <name> [options]
```

## Arguments

| Argument | Type | Required | Description |
|----------|------|----------|-------------|
| `<name>` | string | Yes | - |

## Options

| Option | Type | Required | Default | Description |
|--------|------|----------|---------|-------------|
| `--switch` | boolean | Yes | - | switch to this profile (if more than one) |

## Examples

Create new item:

```bash
bunx @agentuity/cli profile create production
```

Use switch option:

```bash
bunx @agentuity/cli profile create staging --switch
```

Create new item:

```bash
bunx @agentuity/cli profile create development
```

## Output

Returns JSON object:

```json
{
  "success": "boolean",
  "name": "string",
  "path": "string"
}
```

| Field | Type | Description |
|-------|------|-------------|
| `success` | boolean | Whether creation succeeded |
| `name` | string | Profile name |
| `path` | string | Profile file path |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/agentuity) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
