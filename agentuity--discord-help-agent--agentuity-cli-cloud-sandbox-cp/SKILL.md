---
name: agentuity-cli-cloud-sandbox-cp
description: Copy files or directories to or from a sandbox. Requires authentication. Use for Agentuity cloud platform operations Use when this capability is needed.
metadata:
  author: agentuity
---

# Cloud Sandbox Cp

Copy files or directories to or from a sandbox

## Prerequisites

- Authenticated with `agentuity auth login`
- Organization context required (`--org-id` or default org)

## Usage

```bash
agentuity cloud sandbox cp <source> <destination> [options]
```

## Arguments

| Argument | Type | Required | Description |
|----------|------|----------|-------------|
| `<source>` | string | Yes | - |
| `<destination>` | string | Yes | - |

## Options

| Option | Type | Required | Default | Description |
|--------|------|----------|---------|-------------|
| `--timeout` | string | Yes | - | Operation timeout (e.g., "5m", "1h") |
| `--recursive` | boolean | No | `false` | Copy directories recursively |

## Examples

Copy a local file to a sandbox:

```bash
bunx @agentuity/cli cloud sandbox cp ./local-file.txt snbx_abc123:/path/to/file.txt
```

Copy a file from a sandbox to local:

```bash
bunx @agentuity/cli cloud sandbox cp snbx_abc123:/path/to/file.txt ./local-file.txt
```

Copy a local directory to a sandbox recursively:

```bash
bunx @agentuity/cli cloud sandbox cp --recursive ./local-dir snbx_abc123:/path/to/dir
```

Copy a directory from a sandbox to local recursively:

```bash
bunx @agentuity/cli cloud sandbox cp -r snbx_abc123:/path/to/dir ./local-dir
```

## Output

Returns JSON object:

```json
{
  "source": "string",
  "destination": "string",
  "bytesTransferred": "number",
  "filesTransferred": "number"
}
```

| Field | Type | Description |
|-------|------|-------------|
| `source` | string | Source path |
| `destination` | string | Destination path |
| `bytesTransferred` | number | Number of bytes transferred |
| `filesTransferred` | number | Number of files transferred |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/agentuity) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
