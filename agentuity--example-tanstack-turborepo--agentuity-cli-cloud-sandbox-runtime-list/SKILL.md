---
name: agentuity-cli-cloud-sandbox-runtime-list
description: List available sandbox runtimes. Requires authentication. Use for Agentuity cloud platform operations Use when this capability is needed.
metadata:
  author: agentuity
---

# Cloud Sandbox Runtime List

List available sandbox runtimes

## Prerequisites

- Authenticated with `agentuity auth login`
- Organization context required (`--org-id` or default org)

## Usage

```bash
agentuity cloud sandbox runtime list [options]
```

## Options

| Option | Type | Required | Default | Description |
|--------|------|----------|---------|-------------|
| `--limit` | number | Yes | - | Maximum number of results |
| `--offset` | number | Yes | - | Offset for pagination |

## Examples

List all available runtimes:

```bash
bunx @agentuity/cli cloud sandbox runtime list
```

## Output

Returns JSON object:

```json
{
  "runtimes": "array",
  "total": "number"
}
```

| Field | Type | Description |
|-------|------|-------------|
| `runtimes` | array | List of runtimes |
| `total` | number | Total number of runtimes |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/agentuity) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
