---
name: agentuity-cli-cloud-sandbox-snapshot-get
description: Get snapshot details. Requires authentication. Use for Agentuity cloud platform operations Use when this capability is needed.
metadata:
  author: agentuity
---

# Cloud Sandbox Snapshot Get

Get snapshot details

## Prerequisites

- Authenticated with `agentuity auth login`
- Organization context required (`--org-id` or default org)

## Usage

```bash
agentuity cloud sandbox snapshot get <snapshotId>
```

## Arguments

| Argument | Type | Required | Description |
|----------|------|----------|-------------|
| `<snapshotId>` | string | Yes | - |

## Examples

Get details for a snapshot:

```bash
bunx @agentuity/cli cloud sandbox snapshot get snp_abc123
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
  "parentSnapshotId": "unknown",
  "createdAt": "string",
  "downloadUrl": "string",
  "files": "array",
  "sandboxes": "array"
}
```

| Field | Type | Description |
|-------|------|-------------|
| `snapshotId` | string | Snapshot ID |
| `sandboxId` | string | Source sandbox ID |
| `tag` | unknown | Snapshot tag |
| `sizeBytes` | number | Snapshot size in bytes |
| `fileCount` | number | Number of files |
| `parentSnapshotId` | unknown | Parent snapshot ID |
| `createdAt` | string | Creation timestamp |
| `downloadUrl` | string | Presigned download URL |
| `files` | array | Files in snapshot |
| `sandboxes` | array | Attached sandboxes (idle or running) |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/agentuity) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
