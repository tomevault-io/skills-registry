---
name: agentuity-cli-cloud-sandbox-rmdir
description: Remove a directory from a sandbox. Requires authentication. Use for Agentuity cloud platform operations Use when this capability is needed.
metadata:
  author: agentuity
---

# Cloud Sandbox Rmdir

Remove a directory from a sandbox

## Prerequisites

- Authenticated with `agentuity auth login`
- Organization context required (`--org-id` or default org)

## Usage

```bash
agentuity cloud sandbox rmdir <sandboxId> <path> [options]
```

## Arguments

| Argument | Type | Required | Description |
|----------|------|----------|-------------|
| `<sandboxId>` | string | Yes | - |
| `<path>` | string | Yes | - |

## Options

| Option | Type | Required | Default | Description |
|--------|------|----------|---------|-------------|
| `--recursive` | boolean | No | `false` | Remove directory and all contents |

## Examples

Remove an empty directory from the sandbox:

```bash
bunx @agentuity/cli cloud sandbox rmdir sbx_abc123 /path/to/dir
```

Remove a directory and all its contents recursively:

```bash
bunx @agentuity/cli cloud sandbox rmdir sbx_abc123 /path/to/dir -r
```

## Output

Returns JSON object:

```json
{
  "success": "boolean",
  "path": "string"
}
```

| Field | Type | Description |
|-------|------|-------------|
| `success` | boolean | - |
| `path` | string | - |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/agentuity) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-12 -->
