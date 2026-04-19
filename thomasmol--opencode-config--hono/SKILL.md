---
name: hono-documentation-search
description: Use the hono CLI to search and view Hono framework documentation. Use this when planning or building with Hono. Use when this capability is needed.
metadata:
  author: thomasmol
---

# Hono

Use the `hono` CLI for efficient development. View all commands with `hono --help`.

## Instructions

Use `hono docs` and `hono search` commands to access Hono documentation and answer questions about the Hono framework.

- **`hono docs [path]`** - Browse Hono documentation
- **`hono search <query>`** - Search documentation

## Examples

### Search for topics

```bash
hono search middleware
hono search "getting started"
```

### View documentation

```bash
hono docs /docs/api/context
hono docs /docs/guides/middleware
```

### Pipelines

```bash
hono search "middleware" | jq '.results[0].path' | hono docs
hono search "routing" | jq '.results[0].path' | hono docs
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/thomasmol) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
