---
name: agentuity-cli-cloud-thread-delete
description: Delete a thread. Requires authentication. Use for Agentuity cloud platform operations Use when this capability is needed.
metadata:
  author: agentuity
---

# Cloud Thread Delete

Delete a thread

## Prerequisites

- Authenticated with `agentuity auth login`

## Usage

```bash
agentuity cloud thread delete <thread_id>
```

## Arguments

| Argument | Type | Required | Description |
|----------|------|----------|-------------|
| `<thread_id>` | string | Yes | - |

## Examples

Delete a thread by ID:

```bash
bunx @agentuity/cli cloud thread delete thrd_abc123xyz
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/agentuity) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
