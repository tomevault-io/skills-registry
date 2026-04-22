---
name: tool-quality-audit
description: Audit tool integrations for deterministic behavior, error contracts, and logging before agents depend on them. Use when adding or updating tools or MCP servers. Use when this capability is needed.
metadata:
  author: aufrank
---

# Tool Quality Audit

## Overview
Evaluate a tool’s reliability with a checklist and small smoke tests. Emphasize deterministic outputs, clear errors, and stable schemas.

## Quick start
1) Fill `templates/tool_audit.json`.
2) Run smoke tests manually or via your harness.
3) Record findings in `results.json`.

## Core Guidance
- Prefer deterministic checks before LLM-based grading.
- Verify error contracts (consistent codes/messages).
- Validate schemas are stable and documented.
- Record latency and failure modes.

## Resources
- `references/tool-audit-checklist.md`: Reliability and contract checklist.
- `templates/tool_audit.json`: Audit scaffold for a tool.

## Validation
- Ensure audit file is filled and failures are actionable.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/aufrank) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
