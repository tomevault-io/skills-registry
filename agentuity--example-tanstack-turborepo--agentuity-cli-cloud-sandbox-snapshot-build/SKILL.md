---
name: agentuity-cli-cloud-sandbox-snapshot-build
description: Build a snapshot from a declarative file. Requires authentication. Use for Agentuity cloud platform operations Use when this capability is needed.
metadata:
  author: agentuity
---

# Cloud Sandbox Snapshot Build

Build a snapshot from a declarative file

## Prerequisites

- Authenticated with `agentuity auth login`
- Organization context required (`--org-id` or default org)

## Usage

```bash
agentuity cloud sandbox snapshot build <directory> [options]
```

## Arguments

| Argument | Type | Required | Description |
|----------|------|----------|-------------|
| `<directory>` | string | Yes | - |

## Options

| Option | Type | Required | Default | Description |
|--------|------|----------|---------|-------------|
| `--file` | string | Yes | - | Path to build file (defaults to agentuity-snapshot.[json|yaml|yml]) |
| `--env` | array | Yes | - | Environment variable substitution (KEY=VALUE) |
| `--name` | string | Yes | - | Snapshot name (overrides build file) |
| `--tag` | string | Yes | - | Snapshot tag (defaults to "latest") |
| `--description` | string | Yes | - | Snapshot description (overrides build file) |
| `--metadata` | array | Yes | - | Metadata key-value pairs (KEY=VALUE) |
| `--force` | boolean | Yes | - | Force rebuild even if content is unchanged |

## Examples

Build a snapshot from the current directory using agentuity-snapshot.yaml:

```bash
bunx @agentuity/cli cloud sandbox snapshot build .
```

Build using a custom build file:

```bash
bunx @agentuity/cli cloud sandbox snapshot build ./project --file custom-build.yaml
```

Build with environment variable substitution and custom tag:

```bash
bunx @agentuity/cli cloud sandbox snapshot build . --env API_KEY=secret --tag production
```

Validate the build file without uploading:

```bash
bunx @agentuity/cli cloud sandbox snapshot build . --dry-run
```

Force rebuild even if content is unchanged:

```bash
bunx @agentuity/cli cloud sandbox snapshot build . --force
```

## Output

Returns JSON object:

```json
{
  "snapshotId": "string",
  "name": "string",
  "tag": "unknown",
  "runtime": "string",
  "sizeBytes": "number",
  "fileCount": "number",
  "createdAt": "string",
  "unchanged": "boolean",
  "userMetadata": "object"
}
```

| Field | Type | Description |
|-------|------|-------------|
| `snapshotId` | string | Snapshot ID |
| `name` | string | Snapshot name |
| `tag` | unknown | Snapshot tag |
| `runtime` | string | Runtime identifier |
| `sizeBytes` | number | Snapshot size in bytes |
| `fileCount` | number | Number of files in snapshot |
| `createdAt` | string | Snapshot creation timestamp |
| `unchanged` | boolean | True if snapshot was unchanged |
| `userMetadata` | object | User-defined metadata key-value pairs |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/agentuity) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
