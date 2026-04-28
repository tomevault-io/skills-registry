---
name: agentuity-cli-cloud-redis-show
description: Show Redis connection URL. Requires authentication. Use for Agentuity cloud platform operations Use when this capability is needed.
metadata:
  author: agentuity
---

# Cloud Redis Show

Show Redis connection URL

## Prerequisites

- Authenticated with `agentuity auth login`
- Organization context required (`--org-id` or default org)

## Usage

```bash
agentuity cloud redis show [options]
```

## Options

| Option | Type | Required | Default | Description |
|--------|------|----------|---------|-------------|
| `--showCredentials` | boolean | Yes | - | Show credentials in plain text (default: masked in terminal, unmasked in JSON) |

## Examples

Show Redis connection URL:

```bash
bunx @agentuity/cli cloud redis show
```

Show Redis URL with credentials visible:

```bash
bunx @agentuity/cli cloud redis show --show-credentials
```

Show Redis URL as JSON:

```bash
bunx @agentuity/cli --json cloud redis show
```

## Output

Returns JSON object:

```json
{
  "url": "string"
}
```

| Field | Type | Description |
|-------|------|-------------|
| `url` | string | Redis connection URL |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/agentuity) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
