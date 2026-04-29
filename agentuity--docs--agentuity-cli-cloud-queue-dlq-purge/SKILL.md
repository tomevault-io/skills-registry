---
name: agentuity-cli-cloud-queue-dlq-purge
description: Purge all messages from the dead letter queue. Requires authentication. Use for Agentuity cloud platform operations Use when this capability is needed.
metadata:
  author: agentuity
---

# Cloud Queue Dlq Purge

Purge all messages from the dead letter queue

## Prerequisites

- Authenticated with `agentuity auth login`

## Usage

```bash
agentuity cloud queue dlq purge <queue_name> [options]
```

## Arguments

| Argument | Type | Required | Description |
|----------|------|----------|-------------|
| `<queue_name>` | string | Yes | - |

## Options

| Option | Type | Required | Default | Description |
|--------|------|----------|---------|-------------|
| `--confirm` | boolean | No | `false` | Skip confirmation prompt |

## Examples

Purge all DLQ messages:

```bash
bunx @agentuity/cli cloud queue dlq purge my-queue --confirm
```

## Output

Returns JSON object:

```json
{
  "success": "boolean",
  "queue_name": "string"
}
```

| Field | Type | Description |
|-------|------|-------------|
| `success` | boolean | - |
| `queue_name` | string | - |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/agentuity) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-12 -->
