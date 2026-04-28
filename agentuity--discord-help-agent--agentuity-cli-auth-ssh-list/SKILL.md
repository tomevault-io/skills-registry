---
name: agentuity-cli-auth-ssh-list
description: List all SSH keys on your account. Requires authentication. Use for managing authentication credentials Use when this capability is needed.
metadata:
  author: agentuity
---

# Auth Ssh List

List all SSH keys on your account

## Prerequisites

- Authenticated with `agentuity auth login`

## Usage

```bash
agentuity auth ssh list
```

## Examples

List items:

```bash
bunx @agentuity/cli auth ssh list
```

List items:

```bash
bunx @agentuity/cli auth ssh ls
```

Show output in JSON format:

```bash
bunx @agentuity/cli --json auth ssh list
```

## Output

Returns: `array`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/agentuity) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
