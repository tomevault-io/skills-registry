---
name: agentuity-cli-profile-use
description: Switch to a different configuration profile Use when this capability is needed.
metadata:
  author: agentuity
---

# Profile Use

Switch to a different configuration profile

## Usage

```bash
agentuity profile use [name]
```

## Arguments

| Argument | Type | Required | Description |
|----------|------|----------|-------------|
| `<name>` | string | No | - |

## Examples

Switch to the "production" profile:

```bash
bunx @agentuity/cli profile use production
```

Switch to the "staging" profile:

```bash
bunx @agentuity/cli profile switch staging
```

Show interactive profile selection menu:

```bash
bunx @agentuity/cli profile use
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/agentuity) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
