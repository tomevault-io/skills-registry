---
name: agentuity-cli-cloud-stream-delete
description: Delete a stream by ID (soft delete). Requires authentication. Use for Agentuity cloud platform operations Use when this capability is needed.
metadata:
  author: agentuity
---

# Cloud Stream Delete

Delete a stream by ID (soft delete)

## Prerequisites

- Authenticated with `agentuity auth login`
- Project context required (run from project directory or use `--project-id`)

## Usage

```bash
agentuity cloud stream delete <id>
```

## Arguments

| Argument | Type | Required | Description |
|----------|------|----------|-------------|
| `<id>` | string | Yes | - |

## Examples

Delete a stream:

```bash
bunx @agentuity/cli stream delete stream-id-123
```

Delete stream (using alias):

```bash
bunx @agentuity/cli stream rm stream-id-456
```

Delete stream (using alias):

```bash
bunx @agentuity/cli stream del stream-id-789
```

## Output

Returns JSON object:

```json
{
  "id": "string"
}
```

| Field | Type | Description |
|-------|------|-------------|
| `id` | string | Stream ID |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/agentuity) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
