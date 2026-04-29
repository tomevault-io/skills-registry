---
name: agentuity-cli-cloud-queue-sources-list
description: List sources for a queue. Requires authentication. Use for Agentuity cloud platform operations Use when this capability is needed.
metadata:
  author: agentuity
---

# Cloud Queue Sources List

List sources for a queue

## Prerequisites

- Authenticated with `agentuity auth login`

## Usage

```bash
agentuity cloud queue sources list <queue_name>
```

## Arguments

| Argument | Type | Required | Description |
|----------|------|----------|-------------|
| `<queue_name>` | string | Yes | - |

## Examples

List queue sources:

```bash
bunx @agentuity/cli cloud queue sources list my-queue
```

## Output

Returns JSON object:

```json
{
  "sources": "array"
}
```

| Field | Type | Description |
|-------|------|-------------|
| `sources` | array | - |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/agentuity) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-12 -->
