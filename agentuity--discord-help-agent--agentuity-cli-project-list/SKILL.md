---
name: agentuity-cli-project-list
description: List all projects. Requires authentication. Use for project management operations Use when this capability is needed.
metadata:
  author: agentuity
---

# Project List

List all projects

## Prerequisites

- Authenticated with `agentuity auth login`

## Usage

```bash
agentuity project list
```

## Examples

List projects (human-readable):

```bash
bunx @agentuity/cli project list
```

List projects in JSON format:

```bash
bunx @agentuity/cli --json project list
```

Alias for "project list" — list projects (human-readable):

```bash
bunx @agentuity/cli project ls
```

## Output

Returns: `array`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/agentuity) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
