---
name: agentuity-cli-cloud-queue-dlq-replay
description: Replay a message from the dead letter queue. Requires authentication. Use for Agentuity cloud platform operations Use when this capability is needed.
metadata:
  author: agentuity
---

# Cloud Queue Dlq Replay

Replay a message from the dead letter queue

## Prerequisites

- Authenticated with `agentuity auth login`

## Usage

```bash
agentuity cloud queue dlq replay <queue_name> <message_id>
```

## Arguments

| Argument | Type | Required | Description |
|----------|------|----------|-------------|
| `<queue_name>` | string | Yes | - |
| `<message_id>` | string | Yes | - |

## Examples

Replay a DLQ message:

```bash
bunx @agentuity/cli cloud queue dlq replay my-queue msg-123
```

## Output

Returns JSON object:

```json
{
  "success": "boolean",
  "message": "object"
}
```

| Field | Type | Description |
|-------|------|-------------|
| `success` | boolean | - |
| `message` | object | - |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/agentuity) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-12 -->
