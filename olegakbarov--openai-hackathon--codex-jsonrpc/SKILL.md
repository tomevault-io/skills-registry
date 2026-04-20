---
name: codex-jsonrpc
description: Implement or debug the CodexMonitor remote backend daemon and client using the JSON-over-TCP, line-delimited JSON-RPC-ish protocol. Use when building the TypeScript/Node.js daemon, wiring Codex app-server proxy methods, auth handshake, workspace/worktree management, or the matching client. Use when this capability is needed.
metadata:
  author: olegakbarov
---

# Codex JSON-RPC Daemon

## Overview
Use this skill to implement a TypeScript/Node.js remote backend daemon and client that are protocol-compatible with the Rust CodexMonitor implementation. The full specification is in `references/codex-jsonrpc-typescript-spec.md`.

## Implementation workflow
1. Implement TCP server with line-delimited JSON framing.
2. Add auth gating (`auth` first) and error responses.
3. Implement workspace/worktree CRUD and persistence.
4. Spawn and proxy `codex app-server` sessions per workspace.
5. Broadcast app-server events to all authenticated clients.
6. Implement the TypeScript client wrapper for calls and notifications.
7. Verify method names, params, and error messages match spec.

## Protocol checklist
- One JSON object per line; ignore blank or invalid JSON.
- No response if request has no numeric `id`.
- Responses are `{id,result}` or `{id,error:{message}}` with exact messages.
- Notifications: `app-server-event` (snake `workspace_id`) and optional `terminal-output`.

## Codex app-server proxy rules
- Initialize app-server on spawn and emit `codex/connected` event.
- Map accessMode to sandboxPolicy and approvalPolicy exactly.
- Proxy thread/turn/review/model/skills methods as raw JSON.

## Persistence
- `workspaces.json` and `settings.json` in dataDir.
- Atomic writes after each mutation.

## References
- `references/codex-jsonrpc-typescript-spec.md` contains the full spec.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/olegakbarov) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
