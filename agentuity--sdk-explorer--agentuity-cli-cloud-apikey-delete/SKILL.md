---
name: agentuity-cli-cloud-apikey-delete
description: Delete an API key (soft delete). Requires authentication. Use for Agentuity cloud platform operations Use when this capability is needed.
metadata:
  author: agentuity
---

# Cloud Apikey Delete

Delete an API key (soft delete)

## Prerequisites

- Authenticated with `agentuity auth login`

## Usage

```bash
agentuity cloud apikey delete <id>
```

## Arguments

| Argument | Type | Required | Description |
|----------|------|----------|-------------|
| `<id>` | string | Yes | - |

## Examples

Delete item:

```bash
bunx @agentuity/cli cloud apikey delete <id>
```

Run <id> command:

```bash
bunx @agentuity/cli cloud apikey del <id>
```

Delete item:

```bash
bunx @agentuity/cli cloud apikey rm <id>
```

## Output

Returns JSON object:

```json
{
  "success": "boolean",
  "id": "string"
}
```

| Field | Type | Description |
|-------|------|-------------|
| `success` | boolean | Whether the operation succeeded |
| `id` | string | API key id that was deleted |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/agentuity) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
