---
name: qmcp-sync
description: Manage QE Framework global MCP registry and sync shared MCP server definitions to Claude, Codex, and Gemini configs. Use when the user wants one MCP source of truth across clients. Use when this capability is needed.
metadata:
  author: inho-team
---

# Qmcp-sync

## Role
Manage QE's global MCP registry and synchronize shared server definitions into client-specific config files.

## Commands

### Initialize the global registry
```bash
node scripts/qe_mcp.mjs init-registry
```

### Inspect paths and registry health
```bash
node scripts/qe_mcp.mjs doctor
```

### Preview sync
```bash
node scripts/qe_mcp.mjs sync --dry-run
```

### Apply sync
```bash
node scripts/qe_mcp.mjs sync
```

### Sync one client only
```bash
node scripts/qe_mcp.mjs sync --client codex
node scripts/qe_mcp.mjs sync --client gemini
node scripts/qe_mcp.mjs sync --client claude
```

## Registry Behavior

The default registry lives at:

```text
~/.qe/mcp/registry.json
```

It can contain:
- `qeFramework` MCP server
- native MCP servers
- remote MCP servers
- CLI-wrapped servers such as `cli-anything` style adapters

## Secret Handling

If a server needs credentials:
- do not store plaintext tokens in the client config
- store them with `Qsecret`
- reference them through `envSecrets` in the registry

QE will launch the MCP through `qe_secret_launch.mjs` so the child process receives the env vars without exposing the values in config files.

## Expected Behavior
- Use this skill when the user wants one MCP configuration source for Claude, Codex, and Gemini.
- Prefer the QE registry over editing three client config files by hand.
- Explain honestly that client behavior still differs even when the same server is synced to all three.

## Will
- Keep one MCP registry as the source of truth
- Sync shared config to supported clients
- Support generic MCP entries, not only QE's own server

## Will Not
- Claim all clients interpret prompts or tools identically
- Store plaintext secrets in the synced config files

---
> Source: [inho-team/qe-framework](https://github.com/inho-team/qe-framework) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-23 -->
