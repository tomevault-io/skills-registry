---
name: varlock
description: Use this skill to discover machine-readable metadata published by varlock.dev.
metadata:
  author: dmno-dev
---
# Varlock Agent Discovery

Use this skill to discover machine-readable metadata published by varlock.dev.

## Discovery locations

- API Catalog: `https://varlock.dev/.well-known/api-catalog`
- MCP Server Card: `https://varlock.dev/.well-known/mcp/server-card.json`
- Skills index: `https://varlock.dev/.well-known/agent-skills/index.json`

## Guidance

1. Start from the skills index and verify digest integrity.
2. Follow API catalog relations to find service documentation and descriptors.
3. Use Link response headers on the homepage for bootstrap discovery.

---
> Source: [dmno-dev/varlock](https://github.com/dmno-dev/varlock) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-27 -->
