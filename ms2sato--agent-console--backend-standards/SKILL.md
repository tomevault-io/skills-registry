---
name: backend-standards
description: Detailed backend patterns and code examples for server implementation. Use when you need step-by-step guidance or concrete code patterns beyond what the auto-loaded backend rules provide. Use when this capability is needed.
metadata:
  author: ms2sato
---

# Backend Standards (Procedural Guide)

> **Note:** Declarative rules (conventions, directory structure, naming) are in `.claude/rules/backend.md` and auto-loaded for `packages/server/**`. This skill provides detailed code examples and patterns.

## Detailed Documentation

- [backend-standards.md](backend-standards.md) - Full code examples for Hono routes, Valibot validation, logging patterns, service singletons, callback lifecycle, resource cleanup, async patterns
- [websocket-patterns.md](websocket-patterns.md) - WebSocket implementation details: dual architecture setup, message protocol types, broadcast implementation, output buffering code
- [webhook-receiver-patterns.md](webhook-receiver-patterns.md) - Webhook receiver implementation: always-200 pattern, async processing, signature verification, error handling strategy

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ms2sato) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
