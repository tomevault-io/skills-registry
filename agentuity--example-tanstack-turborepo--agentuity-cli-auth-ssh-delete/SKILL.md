---
name: agentuity-cli-auth-ssh-delete
description: Delete an SSH key from your account. Requires authentication. Use for managing authentication credentials Use when this capability is needed.
metadata:
  author: agentuity
---

# Auth Ssh Delete

Delete an SSH key from your account

## Prerequisites

- Authenticated with `agentuity auth login`

## Usage

```bash
agentuity auth ssh delete [fingerprints...] [options]
```

## Arguments

| Argument | Type | Required | Description |
|----------|------|----------|-------------|
| `<fingerprints...>` | array | No | - |

## Options

| Option | Type | Required | Default | Description |
|--------|------|----------|---------|-------------|
| `--confirm` | boolean | No | `true` | prompt for confirmation before deletion |

## Examples

Delete item:

```bash
bunx @agentuity/cli auth ssh delete
```

Delete item:

```bash
bunx @agentuity/cli auth ssh delete <fingerprint>
```

Delete item:

```bash
bunx @agentuity/cli --explain auth ssh delete abc123
```

Delete item:

```bash
bunx @agentuity/cli --dry-run auth ssh delete abc123
```

## Output

Returns JSON object:

```json
{
  "success": "boolean",
  "removed": "number",
  "fingerprints": "array"
}
```

| Field | Type | Description |
|-------|------|-------------|
| `success` | boolean | Whether the operation succeeded |
| `removed` | number | Number of keys removed |
| `fingerprints` | array | Fingerprints of removed keys |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/agentuity) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
