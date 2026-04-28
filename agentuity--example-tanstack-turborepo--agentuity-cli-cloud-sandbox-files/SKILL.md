---
name: agentuity-cli-cloud-sandbox-files
description: List files in a sandbox directory. Requires authentication. Use for Agentuity cloud platform operations Use when this capability is needed.
metadata:
  author: agentuity
---

# Cloud Sandbox Files

List files in a sandbox directory

## Prerequisites

- Authenticated with `agentuity auth login`
- Organization context required (`--org-id` or default org)

## Usage

```bash
agentuity cloud sandbox files <sandboxId> [path] [options]
```

## Arguments

| Argument | Type | Required | Description |
|----------|------|----------|-------------|
| `<sandboxId>` | string | Yes | - |
| `<path>` | string | No | - |

## Options

| Option | Type | Required | Default | Description |
|--------|------|----------|---------|-------------|
| `--long` | boolean | No | `false` | Use long listing format with permissions and timestamps |

## Examples

List files in the sandbox root directory:

```bash
bunx @agentuity/cli cloud sandbox files sbx_abc123
```

List files in a specific directory:

```bash
bunx @agentuity/cli cloud sandbox files sbx_abc123 /path/to/dir
```

List files with permissions and modification time:

```bash
bunx @agentuity/cli cloud sandbox files sbx_abc123 -l
```

List files with JSON output:

```bash
bunx @agentuity/cli cloud sandbox files sbx_abc123 --json
```

## Output

Returns JSON object:

```json
{
  "files": "array",
  "total": "number"
}
```

| Field | Type | Description |
|-------|------|-------------|
| `files` | array | - |
| `total` | number | - |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/agentuity) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
