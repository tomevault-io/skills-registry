---
name: agentuity-cli-cloud-vector-delete
description: Delete one or more vectors by key. Requires authentication. Use for Agentuity cloud platform operations Use when this capability is needed.
metadata:
  author: agentuity
---

# Cloud Vector Delete

Delete one or more vectors by key

## Prerequisites

- Authenticated with `agentuity auth login`
- Project context required (run from project directory or use `--project-id`)

## Usage

```bash
agentuity cloud vector delete <namespace> <keys...> [options]
```

## Arguments

| Argument | Type | Required | Description |
|----------|------|----------|-------------|
| `<namespace>` | string | Yes | - |
| `<keys...>` | array | Yes | - |

## Options

| Option | Type | Required | Default | Description |
|--------|------|----------|---------|-------------|
| `--confirm` | boolean | No | `false` | if true will not prompt for confirmation |

## Examples

Delete a single vector (interactive):

```bash
bunx @agentuity/cli vector delete products chair-001
```

Delete multiple vectors without confirmation:

```bash
bunx @agentuity/cli vector rm knowledge-base doc-123 doc-456 --confirm
```

Bulk delete without confirmation:

```bash
bunx @agentuity/cli vector del embeddings old-profile-1 old-profile-2 --confirm
```

## Output

Returns JSON object:

```json
{
  "success": "boolean",
  "namespace": "string",
  "keys": "array",
  "deleted": "number",
  "durationMs": "number",
  "message": "string"
}
```

| Field | Type | Description |
|-------|------|-------------|
| `success` | boolean | Whether the operation succeeded |
| `namespace` | string | Namespace name |
| `keys` | array | Keys that were deleted |
| `deleted` | number | Number of vectors deleted |
| `durationMs` | number | Operation duration in milliseconds |
| `message` | string | Confirmation message |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/agentuity) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
