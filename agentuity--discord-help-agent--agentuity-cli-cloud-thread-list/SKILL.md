---
name: agentuity-cli-cloud-thread-list
description: List recent threads. Requires authentication. Use for Agentuity cloud platform operations Use when this capability is needed.
metadata:
  author: agentuity
---

# Cloud Thread List

List recent threads

## Prerequisites

- Authenticated with `agentuity auth login`

## Usage

```bash
agentuity cloud thread list [options]
```

## Options

| Option | Type | Required | Default | Description |
|--------|------|----------|---------|-------------|
| `--count` | number | No | `10` | Number of threads to list (1–100) |
| `--orgId` | string | Yes | - | Filter by organization ID |
| `--projectId` | string | Yes | - | Filter by project ID |

## Examples

List 10 most recent threads:

```bash
bunx @agentuity/cli cloud thread list
```

List 25 most recent threads:

```bash
bunx @agentuity/cli cloud thread list --count=25
```

Filter by project:

```bash
bunx @agentuity/cli cloud thread list --project-id=proj_*
```

Filter by organization:

```bash
bunx @agentuity/cli cloud thread list --org-id=org_*
```

## Output

Returns: `array`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/agentuity) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
