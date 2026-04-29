---
name: agentuity-cli-cloud-queue-ack
description: Acknowledge a message (mark as processed). Requires authentication. Use for Agentuity cloud platform operations Use when this capability is needed.
metadata:
  author: agentuity
---

# Cloud Queue Ack

Acknowledge a message (mark as processed)

## Prerequisites

- Authenticated with `agentuity auth login`

## Usage

```bash
agentuity cloud queue ack <queue_name> <message_id>
```

## Arguments

| Argument | Type | Required | Description |
|----------|------|----------|-------------|
| `<queue_name>` | string | Yes | - |
| `<message_id>` | string | Yes | - |

## Examples

Acknowledge a message:

```bash
bunx @agentuity/cli cloud queue ack my-queue msg-123
```

## Output

Returns JSON object:

```json
{
  "success": "boolean",
  "queue_name": "string",
  "message_id": "string"
}
```

| Field | Type | Description |
|-------|------|-------------|
| `success` | boolean | - |
| `queue_name` | string | - |
| `message_id` | string | - |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/agentuity) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-12 -->
