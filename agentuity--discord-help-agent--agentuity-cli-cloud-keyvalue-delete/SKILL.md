---
name: agentuity-cli-cloud-keyvalue-delete
description: Delete a key from the keyvalue storage. Requires authentication. Use for Agentuity cloud platform operations Use when this capability is needed.
metadata:
  author: agentuity
---

# Cloud Keyvalue Delete

Delete a key from the keyvalue storage

## Prerequisites

- Authenticated with `agentuity auth login`
- Project context required (run from project directory or use `--project-id`)

## Usage

```bash
agentuity cloud keyvalue delete <namespace> <key>
```

## Arguments

| Argument | Type | Required | Description |
|----------|------|----------|-------------|
| `<namespace>` | string | Yes | - |
| `<key>` | string | Yes | - |

## Examples

Delete user data:

```bash
bunx @agentuity/cli kv delete production user:123
```

Delete cached session:

```bash
bunx @agentuity/cli kv delete cache session:abc
```

Delete homepage cache (using alias):

```bash
bunx @agentuity/cli kv rm staging cache:homepage
```

## Output

Returns JSON object:

```json
{
  "success": "boolean",
  "namespace": "string",
  "key": "string",
  "durationMs": "number"
}
```

| Field | Type | Description |
|-------|------|-------------|
| `success` | boolean | Whether the operation succeeded |
| `namespace` | string | Namespace name |
| `key` | string | Key name |
| `durationMs` | number | Operation duration in milliseconds |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/agentuity) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
