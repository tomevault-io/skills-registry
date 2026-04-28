---
name: agentuity-cli-cloud-storage-list
description: List storage resources or files in a bucket. Requires authentication. Use for Agentuity cloud platform operations Use when this capability is needed.
metadata:
  author: agentuity
---

# Cloud Storage List

List storage resources or files in a bucket

## Prerequisites

- Authenticated with `agentuity auth login`
- Organization context required (`--org-id` or default org)

## Usage

```bash
agentuity cloud storage list [name] [prefix] [options]
```

## Arguments

| Argument | Type | Required | Description |
|----------|------|----------|-------------|
| `<name>` | string | No | - |
| `<prefix>` | string | No | - |

## Options

| Option | Type | Required | Default | Description |
|--------|------|----------|---------|-------------|
| `--showCredentials` | boolean | Yes | - | Show credentials in plain text (default: masked in terminal, unmasked in JSON) |
| `--nameOnly` | boolean | Yes | - | Print the name only |

## Examples

List items:

```bash
bunx @agentuity/cli cloud storage list
```

List items:

```bash
bunx @agentuity/cli cloud storage list my-bucket
```

List items:

```bash
bunx @agentuity/cli cloud storage list my-bucket path/prefix
```

Show output in JSON format:

```bash
bunx @agentuity/cli --json cloud storage list
```

List items:

```bash
bunx @agentuity/cli cloud storage ls
```

Use show credentials option:

```bash
bunx @agentuity/cli cloud storage list --show-credentials
```

## Output

Returns JSON object:

```json
{
  "buckets": "array",
  "files": "array"
}
```

| Field | Type | Description |
|-------|------|-------------|
| `buckets` | array | List of storage resources |
| `files` | array | List of files in bucket |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/agentuity) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
