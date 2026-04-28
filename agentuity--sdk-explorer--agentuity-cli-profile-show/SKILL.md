---
name: agentuity-cli-profile-show
description: Show the configuration of a profile Use when this capability is needed.
metadata:
  author: agentuity
---

# Profile Show

Show the configuration of a profile

## Usage

```bash
agentuity profile show [name]
```

## Arguments

| Argument | Type | Required | Description |
|----------|------|----------|-------------|
| `<name>` | string | No | - |

## Examples

Show details:

```bash
bunx @agentuity/cli profile show
```

Show details:

```bash
bunx @agentuity/cli profile show production
```

Show output in JSON format:

```bash
bunx @agentuity/cli profile show staging --json
```

## Output

Returns JSON object:

```json
{
  "name": "string",
  "auth": "object",
  "devmode": "object",
  "overrides": "unknown",
  "preferences": "object",
  "gravity": "object"
}
```

| Field | Type | Description |
|-------|------|-------------|
| `name` | string | Profile name |
| `auth` | object | Authentication credentials (managed by login/logout commands) |
| `devmode` | object | Development mode configuration |
| `overrides` | unknown | URL and behavior overrides |
| `preferences` | object | User preferences |
| `gravity` | object | the gravity client information |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/agentuity) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
