---
name: agentuity-cli-cloud-sandbox-rm
description: Remove a file from a sandbox. Requires authentication. Use for Agentuity cloud platform operations Use when this capability is needed.
metadata:
  author: agentuity
---

# Cloud Sandbox Rm

Remove a file from a sandbox

## Prerequisites

- Authenticated with `agentuity auth login`
- Organization context required (`--org-id` or default org)

## Usage

```bash
agentuity cloud sandbox rm <sandboxId> <path>
```

## Arguments

| Argument | Type | Required | Description |
|----------|------|----------|-------------|
| `<sandboxId>` | string | Yes | - |
| `<path>` | string | Yes | - |

## Examples

Remove a file from the sandbox:

```bash
bunx @agentuity/cli cloud sandbox rm sbx_abc123 /path/to/file.txt
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
<!-- tomevault:4.0:skill_md:2026-04-14 -->
