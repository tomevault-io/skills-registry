---
name: agentuity-cli-cloud-queue-destinations-update
description: Update a destination. Requires authentication. Use for Agentuity cloud platform operations Use when this capability is needed.
metadata:
  author: agentuity
---

# Cloud Queue Destinations Update

Update a destination

## Prerequisites

- Authenticated with `agentuity auth login`

## Usage

```bash
agentuity cloud queue destinations update <queue_name> <destination_id> [options]
```

## Arguments

| Argument | Type | Required | Description |
|----------|------|----------|-------------|
| `<queue_name>` | string | Yes | - |
| `<destination_id>` | string | Yes | - |

## Options

| Option | Type | Required | Default | Description |
|--------|------|----------|---------|-------------|
| `--url` | string | Yes | - | Webhook URL |
| `--method` | string | Yes | - | HTTP method |
| `--timeout` | number | Yes | - | Request timeout in milliseconds |
| `--enabled` | boolean | Yes | - | Enable the destination |
| `--disabled` | boolean | Yes | - | Disable the destination |

## Examples

Disable a destination:

```bash
bunx @agentuity/cli cloud queue destinations update my-queue dest_abc123 --disabled
```

## Output

Returns JSON object:

```json
{
  "id": "string",
  "queue_id": "string",
  "destination_type": "string",
  "config": "object",
  "enabled": "boolean",
  "stats": "object",
  "created_at": "string",
  "updated_at": "string"
}
```

| Field | Type | Description |
|-------|------|-------------|
| `id` | string | - |
| `queue_id` | string | - |
| `destination_type` | string | - |
| `config` | object | - |
| `enabled` | boolean | - |
| `stats` | object | - |
| `created_at` | string | - |
| `updated_at` | string | - |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/agentuity) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-12 -->
