---
name: agentuity-cli-cloud-sandbox-snapshot-create
description: Create a snapshot from a sandbox. Requires authentication. Use for Agentuity cloud platform operations Use when this capability is needed.
metadata:
  author: agentuity
---

# Cloud Sandbox Snapshot Create

Create a snapshot from a sandbox

## Prerequisites

- Authenticated with `agentuity auth login`
- Organization context required (`--org-id` or default org)

## Usage

```bash
agentuity cloud sandbox snapshot create <sandboxId> [options]
```

## Arguments

| Argument | Type | Required | Description |
|----------|------|----------|-------------|
| `<sandboxId>` | string | Yes | - |

## Options

| Option | Type | Required | Default | Description |
|--------|------|----------|---------|-------------|
| `--tag` | string | Yes | - | Tag for the snapshot |

## Examples

Create a snapshot from a sandbox:

```bash
bunx @agentuity/cli cloud sandbox snapshot create sbx_abc123
```

Create a tagged snapshot:

```bash
bunx @agentuity/cli cloud sandbox snapshot create sbx_abc123 --tag latest
```

## Output

Returns JSON object:

```json
{
  "snapshotId": "string",
  "sandboxId": "string",
  "tag": "unknown",
  "sizeBytes": "number",
  "fileCount": "number",
  "createdAt": "string"
}
```

| Field | Type | Description |
|-------|------|-------------|
| `snapshotId` | string | Snapshot ID |
| `sandboxId` | string | Source sandbox ID |
| `tag` | unknown | Snapshot tag |
| `sizeBytes` | number | Snapshot size in bytes |
| `fileCount` | number | Number of files in snapshot |
| `createdAt` | string | Snapshot creation timestamp |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/agentuity) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
