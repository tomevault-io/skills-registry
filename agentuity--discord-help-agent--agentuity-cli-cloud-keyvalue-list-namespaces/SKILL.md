---
name: agentuity-cli-cloud-keyvalue-list-namespaces
description: List all keyvalue namespaces. Requires authentication. Use for Agentuity cloud platform operations Use when this capability is needed.
metadata:
  author: agentuity
---

# Cloud Keyvalue List-namespaces

List all keyvalue namespaces

## Prerequisites

- Authenticated with `agentuity auth login`
- Project context required (run from project directory or use `--project-id`)

## Usage

```bash
agentuity cloud keyvalue list-namespaces
```

## Examples

List all namespaces:

```bash
bunx @agentuity/cli kv list-namespaces
```

List namespaces (using alias):

```bash
bunx @agentuity/cli kv namespaces
```

List namespaces (short alias):

```bash
bunx @agentuity/cli kv ns
```

## Output

Returns: `array`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/agentuity) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
