---
name: agentuity-cli-cloud-queue-list
description: List all queues. Requires authentication. Use for Agentuity cloud platform operations Use when this capability is needed.
metadata:
  author: agentuity
---

# Cloud Queue List

List all queues

## Prerequisites

- Authenticated with `agentuity auth login`

## Usage

```bash
agentuity cloud queue list [options]
```

## Options

| Option | Type | Required | Default | Description |
|--------|------|----------|---------|-------------|
| `--limit` | number | Yes | - | Maximum number of queues to return |
| `--offset` | number | Yes | - | Offset for pagination |

## Examples

List all queues:

```bash
bunx @agentuity/cli cloud queue list
```

List all queues (alias):

```bash
bunx @agentuity/cli cloud queue ls
```

## Output

Returns JSON object:

```json
{
  "queues": "array",
  "total": "number"
}
```

| Field | Type | Description |
|-------|------|-------------|
| `queues` | array | - |
| `total` | number | - |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/agentuity) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-12 -->
