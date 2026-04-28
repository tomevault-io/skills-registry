---
name: agentuity-cli-cloud-sandbox-delete
description: Delete a sandbox. Requires authentication. Use for Agentuity cloud platform operations Use when this capability is needed.
metadata:
  author: agentuity
---

# Cloud Sandbox Delete

Delete a sandbox

## Prerequisites

- Authenticated with `agentuity auth login`
- Organization context required (`--org-id` or default org)

## Usage

```bash
agentuity cloud sandbox delete <sandboxId> [options]
```

## Arguments

| Argument | Type | Required | Description |
|----------|------|----------|-------------|
| `<sandboxId>` | string | Yes | - |

## Options

| Option | Type | Required | Default | Description |
|--------|------|----------|---------|-------------|
| `--confirm` | boolean | No | `false` | Skip confirmation prompt |

## Examples

Delete a sandbox:

```bash
bunx @agentuity/cli cloud sandbox delete abc123
```

Delete using alias:

```bash
bunx @agentuity/cli cloud sandbox rm abc123
```

Delete without confirmation prompt:

```bash
bunx @agentuity/cli cloud sandbox rm abc123 --confirm
```

## Output

Returns JSON object:

```json
{
  "success": "boolean",
  "sandboxId": "string",
  "durationMs": "number",
  "message": "string"
}
```

| Field | Type | Description |
|-------|------|-------------|
| `success` | boolean | Whether the operation succeeded |
| `sandboxId` | string | Sandbox ID |
| `durationMs` | number | Operation duration in milliseconds |
| `message` | string | Status message |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/agentuity) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
