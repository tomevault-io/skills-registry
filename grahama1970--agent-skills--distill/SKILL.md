---
name: distill
description: > Use when this capability is needed.
metadata:
  author: grahama1970
---

# distill (compatibility)

This skill exists for backward compatibility. Forwards to `/doc2qra`.

- Legacy callers (for example `memory acquire content`) still invoke `distill`.
- The shim forwards all arguments directly to `/doc2qra`.

Supported flags (passed through to doc2qra):

- `--file` — PDF, markdown, or text file
- `--url` — URL to fetch and distill
- `--text` — Raw text to distill
- `--scope` — Memory scope
- `--persona` — Persona for quality gating
- `--json` — JSON output
- `--dry-run` — Preview without storing
- `--context` — Domain focus

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/grahama1970) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
