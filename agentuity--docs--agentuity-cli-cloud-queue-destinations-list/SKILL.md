---
name: agentuity-cli-cloud-queue-destinations-list
description: List destinations for a queue. Requires authentication. Use for Agentuity cloud platform operations Use when this capability is needed.
metadata:
  author: agentuity
---

# Cloud Queue Destinations List

List destinations for a queue

## Prerequisites

- Authenticated with `agentuity auth login`

## Usage

```bash
agentuity cloud queue destinations list <queue_name>
```

## Arguments

| Argument | Type | Required | Description |
|----------|------|----------|-------------|
| `<queue_name>` | string | Yes | - |

## Examples

List queue destinations:

```bash
bunx @agentuity/cli cloud queue destinations list my-queue
```

## Output

Returns JSON object:

```json
{
  "destinations": "array"
}
```

| Field | Type | Description |
|-------|------|-------------|
| `destinations` | array | - |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/agentuity) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-12 -->
