---
name: agentuity-cli-cloud-queue-nack
description: Negative acknowledge a message (return to queue for retry). Requires authentication. Use for Agentuity cloud platform operations Use when this capability is needed.
metadata:
  author: agentuity
---

# Cloud Queue Nack

Negative acknowledge a message (return to queue for retry)

## Prerequisites

- Authenticated with `agentuity auth login`

## Usage

```bash
agentuity cloud queue nack <queue_name> <message_id>
```

## Arguments

| Argument | Type | Required | Description |
|----------|------|----------|-------------|
| `<queue_name>` | string | Yes | - |
| `<message_id>` | string | Yes | - |

## Examples

Return message to queue for retry:

```bash
bunx @agentuity/cli cloud queue nack my-queue msg-123
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
