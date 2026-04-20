---
name: bolta-audit-export-activity
description: Export audit/activity events for a workspace (agent + human actions) for debugging and compliance. Use when this capability is needed.
metadata:
  author: boltaai
---

## Steps
1. Call `bolta.export_audit_log({workspace_id, start_time, end_time, actor_filter})`
2. Summarize:
   - top actions
   - failures
   - suspicious patterns (many denied attempts, repeated schedule calls)

## Output
Return events + a short summary.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/boltaai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
