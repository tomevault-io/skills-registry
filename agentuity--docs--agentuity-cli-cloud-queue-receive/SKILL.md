---
name: agentuity-cli-cloud-queue-receive
description: Receive (claim) a message from a worker queue. Requires authentication. Use for Agentuity cloud platform operations Use when this capability is needed.
metadata:
  author: agentuity
---

# Cloud Queue Receive

Receive (claim) a message from a worker queue

## Prerequisites

- Authenticated with `agentuity auth login`

## Usage

```bash
agentuity cloud queue receive <queue_name> [options]
```

## Arguments

| Argument | Type | Required | Description |
|----------|------|----------|-------------|
| `<queue_name>` | string | Yes | - |

## Options

| Option | Type | Required | Default | Description |
|--------|------|----------|---------|-------------|
| `--timeout` | number | No | `30` | Visibility timeout in seconds (default: 30) |

## Examples

Receive next message:

```bash
bunx @agentuity/cli cloud queue receive my-queue
```

Receive with 60s visibility timeout:

```bash
bunx @agentuity/cli cloud queue receive my-queue --timeout 60
```

## Output

Returns JSON object:

```json
{
  "message": "unknown"
}
```

| Field | Type | Description |
|-------|------|-------------|
| `message` | unknown | - |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/agentuity) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-12 -->
