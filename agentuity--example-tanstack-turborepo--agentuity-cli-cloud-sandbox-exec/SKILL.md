---
name: agentuity-cli-cloud-sandbox-exec
description: Execute a command in a running sandbox. Requires authentication. Use for Agentuity cloud platform operations Use when this capability is needed.
metadata:
  author: agentuity
---

# Cloud Sandbox Exec

Execute a command in a running sandbox

## Prerequisites

- Authenticated with `agentuity auth login`
- Organization context required (`--org-id` or default org)

## Usage

```bash
agentuity cloud sandbox exec <sandboxId> <command...> [options]
```

## Arguments

| Argument | Type | Required | Description |
|----------|------|----------|-------------|
| `<sandboxId>` | string | Yes | - |
| `<command...>` | array | Yes | - |

## Options

| Option | Type | Required | Default | Description |
|--------|------|----------|---------|-------------|
| `--timeout` | string | Yes | - | Execution timeout (e.g., "5m", "1h") |
| `--timestamps` | boolean | No | `false` | Include timestamps in output (default: false) |

## Examples

Execute a command in a sandbox:

```bash
bunx @agentuity/cli cloud sandbox exec abc123 -- echo "hello"
```

Execute with timeout:

```bash
bunx @agentuity/cli cloud sandbox exec abc123 --timeout 5m -- bun run build
```

## Output

Returns JSON object:

```json
{
  "executionId": "string",
  "status": "string",
  "exitCode": "number",
  "durationMs": "number",
  "output": "string"
}
```

| Field | Type | Description |
|-------|------|-------------|
| `executionId` | string | Unique execution identifier |
| `status` | string | Execution status |
| `exitCode` | number | Exit code (if completed) |
| `durationMs` | number | Duration in milliseconds (if completed) |
| `output` | string | Combined stdout/stderr output |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/agentuity) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
