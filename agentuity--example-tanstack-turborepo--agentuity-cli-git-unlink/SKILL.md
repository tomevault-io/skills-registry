---
name: agentuity-cli-git-unlink
description: Unlink a project from its GitHub repository. Requires authentication Use when this capability is needed.
metadata:
  author: agentuity
---

# Git Unlink

Unlink a project from its GitHub repository

## Prerequisites

- Authenticated with `agentuity auth login`
- Project context required (run from project directory or use `--project-id`)

## Usage

```bash
agentuity git unlink [options]
```

## Options

| Option | Type | Required | Default | Description |
|--------|------|----------|---------|-------------|
| `--confirm` | boolean | Yes | - | Skip confirmation prompt |

## Examples

Unlink current project from GitHub:

```bash
bunx @agentuity/cli git unlink
```

Unlink without confirmation prompt:

```bash
bunx @agentuity/cli git unlink --confirm
```

Unlink and return JSON result:

```bash
bunx @agentuity/cli --json git unlink --confirm
```

## Output

Returns JSON object:

```json
{
  "unlinked": "boolean",
  "repoFullName": "string"
}
```

| Field | Type | Description |
|-------|------|-------------|
| `unlinked` | boolean | Whether the project was unlinked |
| `repoFullName` | string | Repository that was unlinked |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/agentuity) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
