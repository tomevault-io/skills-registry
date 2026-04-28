---
name: agentuity-cli-git-account-list
description: List GitHub accounts connected to your organizations. Requires authentication Use when this capability is needed.
metadata:
  author: agentuity
---

# Git Account List

List GitHub accounts connected to your organizations

## Prerequisites

- Authenticated with `agentuity auth login`

## Usage

```bash
agentuity git account list
```

## Examples

List all connected GitHub accounts:

```bash
bunx @agentuity/cli git account list
```

List accounts in JSON format:

```bash
bunx @agentuity/cli --json git account list
```

## Output

Returns: `array`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/agentuity) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
