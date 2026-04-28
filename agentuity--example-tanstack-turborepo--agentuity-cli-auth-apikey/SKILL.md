---
name: agentuity-cli-auth-apikey
description: Display the API key for the currently authenticated user. Requires authentication. Use for managing authentication credentials Use when this capability is needed.
metadata:
  author: agentuity
---

# Auth Apikey

Display the API key for the currently authenticated user

## Prerequisites

- Authenticated with `agentuity auth login`

## Usage

```bash
agentuity auth apikey
```

## Examples

Print the API key:

```bash
bunx @agentuity/cli auth apikey
```

Output API key in JSON format:

```bash
bunx @agentuity/cli --json auth apikey
```

## Output

Returns JSON object:

```json
{
  "apiKey": "string"
}
```

| Field | Type | Description |
|-------|------|-------------|
| `apiKey` | string | The API key for the authenticated user |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/agentuity) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
