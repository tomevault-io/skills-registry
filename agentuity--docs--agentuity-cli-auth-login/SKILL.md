---
name: agentuity-cli-auth-login
description: Login to the Agentuity Platform using a browser-based authentication flow. Use for managing authentication credentials Use when this capability is needed.
metadata:
  author: agentuity
---

# Auth Login

Login to the Agentuity Platform using a browser-based authentication flow

## Usage

```bash
agentuity auth login [options]
```

## Options

| Option | Type | Required | Default | Description |
|--------|------|----------|---------|-------------|
| `--setupToken` | string | Yes | - | Use a one-time use setup token |

## Examples

Login to account:

```bash
bunx @agentuity/cli auth login
```

Login to account:

```bash
bunx @agentuity/cli login
```

## Output

Returns JSON object:

```json
{
  "success": "boolean"
}
```

| Field | Type | Description |
|-------|------|-------------|
| `success` | boolean | - |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/agentuity) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-12 -->
