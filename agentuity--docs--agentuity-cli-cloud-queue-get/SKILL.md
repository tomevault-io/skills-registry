---
name: agentuity-cli-cloud-queue-get
description: Get queue or message details. Requires authentication. Use for Agentuity cloud platform operations Use when this capability is needed.
metadata:
  author: agentuity
---

# Cloud Queue Get

Get queue or message details

## Prerequisites

- Authenticated with `agentuity auth login`

## Usage

```bash
agentuity cloud queue get <queue_name> [message_id]
```

## Arguments

| Argument | Type | Required | Description |
|----------|------|----------|-------------|
| `<queue_name>` | string | Yes | - |
| `<message_id>` | string | No | - |

## Examples

Get queue details:

```bash
bunx @agentuity/cli cloud queue get my-queue
```

Get message details:

```bash
bunx @agentuity/cli cloud queue get my-queue msg_abc123
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/agentuity) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-12 -->
