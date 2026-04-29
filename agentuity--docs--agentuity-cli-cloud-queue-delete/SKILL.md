---
name: agentuity-cli-cloud-queue-delete
description: Delete a queue by name. Requires authentication. Use for Agentuity cloud platform operations Use when this capability is needed.
metadata:
  author: agentuity
---

# Cloud Queue Delete

Delete a queue by name

## Prerequisites

- Authenticated with `agentuity auth login`

## Usage

```bash
agentuity cloud queue delete <name> [options]
```

## Arguments

| Argument | Type | Required | Description |
|----------|------|----------|-------------|
| `<name>` | string | Yes | - |

## Options

| Option | Type | Required | Default | Description |
|--------|------|----------|---------|-------------|
| `--confirm` | boolean | No | `false` | Skip confirmation prompt |

## Examples

Delete a queue (requires confirmation):

```bash
bunx @agentuity/cli cloud queue delete my-queue --confirm
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
| `success` | boolean | - |
| `name` | string | - |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/agentuity) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-12 -->
