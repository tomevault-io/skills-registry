---
name: sovmem-health
description: Health check for sovereign-memory used by Codex (MCP availability, deterministic recall, log freshness, and backups presence). Soft gate by default. Use when this capability is needed.
metadata:
  author: sonra44
---

# Sovereign Memory — Health Check (Soft Gate)

## Goal
Confirm “context will not be lost” assumptions before trusting memory-driven workflows.

## Procedure

1) MCP liveness:
   - `mcp__sovereign-memory__recall_by_tags(project="SERVER", topic="PROTOCOL_WORKFLOW", limit=3)`

2) Smoke checks (preferred evidence):
   - Read freshness of `~/logs/sovereign-memory-smoke.log` (hourly no-write).
   - Read freshness of `~/logs/sovereign-memory-smoke-write.log` (daily write-check).

3) Backups:
   - Verify `/opt/backups/memory_YYYY-MM-DD.db` exists for today (or last 24h).
   - Verify `/opt/backups/memori_YYYY-MM-DD.tar.gz` exists for today (or last 24h).

## Output
- OK or WARN for each of: MCP, smoke(no-write), smoke(write), backups(DB), backups(MEMORI).
- If WARN: provide the exact fix command(s) and where to look for logs.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sonra44) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
