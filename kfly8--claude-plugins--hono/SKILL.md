---
name: hono
description: Assist with Hono web framework development using the hono CLI for documentation search, browsing, and testing requests without starting a server. Use when this capability is needed.
metadata:
  author: kfly8
---

# Hono Development

Use the `hono` CLI for efficient development. View all commands with `hono --help`.

## Core Commands

- **`hono docs [path]`** - Browse Hono documentation
- **`hono search <query>`** - Search documentation
- **`hono request [file]`** - Test app requests without starting a server

## Quick Examples

```bash
# Search for topics
hono search middleware
hono search "getting started"

# View documentation
hono docs /docs/api/context
hono docs /docs/guides/middleware

# Test your app
hono request -P /api/users src/index.ts
hono request -P /api/users -X POST -d '{"name":"Alice"}' src/index.ts
```

## Workflow

1. Search documentation: `hono search <query>`
2. Read relevant docs: `hono docs [path]`
3. Test implementation: `hono request [file]`

## Guidelines

- Always use the `hono` CLI commands for documentation lookup instead of web searches
- Prefer `hono request` for testing endpoints during development
- Search documentation first before implementing features
- Follow the workflow: search → read docs → test implementation

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kfly8) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
