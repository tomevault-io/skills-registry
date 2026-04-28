---
name: agentuity-cli-cloud-keyvalue-delete-namespace
description: Delete a keyvalue namespace and all its keys. Requires authentication. Use for Agentuity cloud platform operations Use when this capability is needed.
metadata:
  author: agentuity
---

# Cloud Keyvalue Delete-namespace

Delete a keyvalue namespace and all its keys

## Prerequisites

- Authenticated with `agentuity auth login`
- Project context required (run from project directory or use `--project-id`)

## Usage

```bash
agentuity cloud keyvalue delete-namespace <name> <confirm>
```

## Arguments

| Argument | Type | Required | Description |
|----------|------|----------|-------------|
| `<name>` | string | Yes | - |
| `<confirm>` | string | Yes | - |

## Examples

Delete staging namespace (interactive):

```bash
bunx @agentuity/cli kv delete-namespace staging
```

Delete cache without confirmation:

```bash
bunx @agentuity/cli kv rm-namespace cache --confirm
```

Force delete production:

```bash
bunx @agentuity/cli kv delete-namespace production --confirm
```

## Output

Returns JSON object:

```json
{
  "success": "boolean",
  "namespace": "string",
  "message": "string"
}
```

| Field | Type | Description |
|-------|------|-------------|
| `success` | boolean | Whether the deletion succeeded |
| `namespace` | string | Deleted namespace name |
| `message` | string | Confirmation message |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/agentuity) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
