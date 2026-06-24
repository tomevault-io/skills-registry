---
name: crumb-doctor
description: >- Use when this capability is needed.
metadata:
  author: mangowhoiscloud
---

# /crumb-doctor — environment health check

When the user asks about environment readiness or what presets are viable:

**Preferred path** — call the `crumb_doctor` MCP tool (no args).

**Fallback path** — `npx tsx src/index.ts doctor`.

Output table: `| Host | Status | Detail |` for Claude Code / Codex CLI / Gemini CLI / playwright / htmlhint. Then `## Recommended preset (for the current environment)` with viable / not-viable per preset.

Explicit slash form: `/crumb-doctor`.

Reference: `src/helpers/doctor.ts`, v0.1 §3 (auth-manager spec).

---
> Source: [mangowhoiscloud/crumb](https://github.com/mangowhoiscloud/crumb) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-23 -->
