---
name: agentuity-cli-cloud-storage-create
description: Create a new storage resource. Requires authentication. Use for Agentuity cloud platform operations Use when this capability is needed.
metadata:
  author: agentuity
---

# Cloud Storage Create

Create a new storage resource

## Prerequisites

- Authenticated with `agentuity auth login`
- Organization context required (`--org-id` or default org)

## Usage

```bash
agentuity cloud storage create
```

## Examples

Create a new cloud storage bucket:

```bash
bunx @agentuity/cli cloud storage create
```

Alias for "cloud storage create" (shorthand "new"):

```bash
bunx @agentuity/cli cloud storage new
```

Dry-run: display what would be created without making changes:

```bash
bunx @agentuity/cli --dry-run cloud storage create
```

## Output

Returns JSON object:

```json
{
  "success": "boolean",
  "name": "string"
}
```

| Field | Type | Description |
|-------|------|-------------|
| `success` | boolean | Whether creation succeeded |
| `name` | string | Created storage bucket name |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/agentuity) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
