---
name: agentuity-cli-cloud-queue-sources-create
description: Create a source for a queue. Requires authentication. Use for Agentuity cloud platform operations Use when this capability is needed.
metadata:
  author: agentuity
---

# Cloud Queue Sources Create

Create a source for a queue

## Prerequisites

- Authenticated with `agentuity auth login`

## Usage

```bash
agentuity cloud queue sources create <queue_name> [options]
```

## Arguments

| Argument | Type | Required | Description |
|----------|------|----------|-------------|
| `<queue_name>` | string | Yes | - |

## Options

| Option | Type | Required | Default | Description |
|--------|------|----------|---------|-------------|
| `--name` | string | Yes | - | Source name |
| `--description` | string | Yes | - | Source description |
| `--auth-type` | string | No | `"none"` | Authentication type |
| `--auth-value` | string | Yes | - | Authentication value |

## Examples

Create a source with header authentication:

```bash
bunx @agentuity/cli cloud queue sources create my-queue --name webhook-1 --auth-type header --auth-value "X-API-Key:secret123"
```

## Output

Returns JSON object:

```json
{
  "id": "string",
  "queue_id": "string",
  "name": "string",
  "description": "unknown",
  "auth_type": "string",
  "enabled": "boolean",
  "url": "string",
  "request_count": "number",
  "success_count": "number",
  "failure_count": "number",
  "last_request_at": "unknown",
  "last_success_at": "unknown",
  "last_failure_at": "unknown",
  "last_failure_error": "unknown",
  "created_at": "string",
  "updated_at": "string"
}
```

| Field | Type | Description |
|-------|------|-------------|
| `id` | string | - |
| `queue_id` | string | - |
| `name` | string | - |
| `description` | unknown | - |
| `auth_type` | string | - |
| `enabled` | boolean | - |
| `url` | string | - |
| `request_count` | number | - |
| `success_count` | number | - |
| `failure_count` | number | - |
| `last_request_at` | unknown | - |
| `last_success_at` | unknown | - |
| `last_failure_at` | unknown | - |
| `last_failure_error` | unknown | - |
| `created_at` | string | - |
| `updated_at` | string | - |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/agentuity) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-12 -->
