---
name: agentuity-cli-cloud-queue-dlq-list
description: List messages in the dead letter queue. Requires authentication. Use for Agentuity cloud platform operations Use when this capability is needed.
metadata:
  author: agentuity
---

# Cloud Queue Dlq List

List messages in the dead letter queue

## Prerequisites

- Authenticated with `agentuity auth login`

## Usage

```bash
agentuity cloud queue dlq list <queue_name> [options]
```

## Arguments

| Argument | Type | Required | Description |
|----------|------|----------|-------------|
| `<queue_name>` | string | Yes | - |

## Options

| Option | Type | Required | Default | Description |
|--------|------|----------|---------|-------------|
| `--limit` | number | Yes | - | Maximum number of messages to return |
| `--offset` | number | Yes | - | Offset for pagination |

## Examples

List DLQ messages:

```bash
bunx @agentuity/cli cloud queue dlq list my-queue
```

## Output

Returns JSON object:

```json
{
  "messages": "array",
  "total": "number"
}
```

| Field | Type | Description |
|-------|------|-------------|
| `messages` | array | - |
| `total` | number | - |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/agentuity) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-12 -->
