---
name: agentuity-cli-cloud-queue-sources-delete
description: Delete a source from a queue. Requires authentication. Use for Agentuity cloud platform operations Use when this capability is needed.
metadata:
  author: agentuity
---

# Cloud Queue Sources Delete

Delete a source from a queue

## Prerequisites

- Authenticated with `agentuity auth login`

## Usage

```bash
agentuity cloud queue sources delete <queue_name> <source_id>
```

## Arguments

| Argument | Type | Required | Description |
|----------|------|----------|-------------|
| `<queue_name>` | string | Yes | - |
| `<source_id>` | string | Yes | - |

## Examples

Delete a source:

```bash
bunx @agentuity/cli cloud queue sources delete my-queue qsrc_abc123
```

## Output

Returns JSON object:

```json
{
  "success": "boolean",
  "queue_name": "string",
  "source_id": "string"
}
```

| Field | Type | Description |
|-------|------|-------------|
| `success` | boolean | - |
| `queue_name` | string | - |
| `source_id` | string | - |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/agentuity) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-12 -->
