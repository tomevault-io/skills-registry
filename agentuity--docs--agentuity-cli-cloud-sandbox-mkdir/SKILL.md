---
name: agentuity-cli-cloud-sandbox-mkdir
description: Create a directory in a sandbox. Requires authentication. Use for Agentuity cloud platform operations Use when this capability is needed.
metadata:
  author: agentuity
---

# Cloud Sandbox Mkdir

Create a directory in a sandbox

## Prerequisites

- Authenticated with `agentuity auth login`
- Organization context required (`--org-id` or default org)

## Usage

```bash
agentuity cloud sandbox mkdir <sandboxId> <path> [options]
```

## Arguments

| Argument | Type | Required | Description |
|----------|------|----------|-------------|
| `<sandboxId>` | string | Yes | - |
| `<path>` | string | Yes | - |

## Options

| Option | Type | Required | Default | Description |
|--------|------|----------|---------|-------------|
| `--parents` | boolean | No | `false` | Create parent directories as needed |

## Examples

Create a directory in the sandbox:

```bash
bunx @agentuity/cli cloud sandbox mkdir sbx_abc123 /path/to/dir
```

Create nested directories recursively:

```bash
bunx @agentuity/cli cloud sandbox mkdir sbx_abc123 /path/to/nested/dir -p
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
