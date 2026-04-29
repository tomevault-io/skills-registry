---
name: agentuity-cli-cloud-queue-messages
description: List messages in a queue or get a specific message. Requires authentication. Use for Agentuity cloud platform operations Use when this capability is needed.
metadata:
  author: agentuity
---

# Cloud Queue Messages

List messages in a queue or get a specific message

## Prerequisites

- Authenticated with `agentuity auth login`

## Usage

```bash
agentuity cloud queue messages <queue_name> [message_id] [options]
```

## Arguments

| Argument | Type | Required | Description |
|----------|------|----------|-------------|
| `<queue_name>` | string | Yes | - |
| `<message_id>` | string | No | - |

## Options

| Option | Type | Required | Default | Description |
|--------|------|----------|---------|-------------|
| `--limit` | number | Yes | - | Maximum number of messages to return |
| `--offset` | number | Yes | - | Offset for pagination |

## Examples

List messages in a queue:

```bash
bunx @agentuity/cli cloud queue messages my-queue
```

List first 10 messages:

```bash
bunx @agentuity/cli cloud queue messages my-queue --limit 10
```

Get a specific message by ID:

```bash
bunx @agentuity/cli cloud queue messages my-queue msg_abc123
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/agentuity) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-12 -->
