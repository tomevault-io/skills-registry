---
name: agentuity-cli-cloud-thread-get
description: Get details about a specific thread. Requires authentication. Use for Agentuity cloud platform operations Use when this capability is needed.
metadata:
  author: agentuity
---

# Cloud Thread Get

Get details about a specific thread

## Prerequisites

- Authenticated with `agentuity auth login`

## Usage

```bash
agentuity cloud thread get <thread_id>
```

## Arguments

| Argument | Type | Required | Description |
|----------|------|----------|-------------|
| `<thread_id>` | string | Yes | - |

## Examples

Get a thread by ID:

```bash
bunx @agentuity/cli cloud thread get thrd_abc123xyz
```

## Output

Returns JSON object:

```json
{
  "id": "string",
  "created_at": "string",
  "updated_at": "string",
  "deleted": "boolean",
  "deleted_at": "unknown",
  "deleted_by": "unknown",
  "org_id": "string",
  "project_id": "string",
  "user_data": "unknown"
}
```

| Field | Type | Description |
|-------|------|-------------|
| `id` | string | Thread ID |
| `created_at` | string | Creation timestamp |
| `updated_at` | string | Update timestamp |
| `deleted` | boolean | Deleted status |
| `deleted_at` | unknown | Deletion timestamp |
| `deleted_by` | unknown | Deleted by |
| `org_id` | string | Organization ID |
| `project_id` | string | Project ID |
| `user_data` | unknown | User data as JSON |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/agentuity) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
