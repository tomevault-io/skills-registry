---
name: sample-plugin
description: Phase 1 sample plugin synthesizing a SKILL.md frontmatter for backwards-compat tests. Use when this capability is needed.
metadata:
  author: nexu-io
---

# Sample Plugin

This is the SKILL.md half of the Phase 1 e2e fixture. The companion
`open-design.json` sidecar carries the canonical Open Design plugin
manifest fields; this file proves the SKILL-only adapter path stays
honest when an install lacks an explicit sidecar (just delete
`open-design.json` to test the legacy compat tier).

## Workflow

1. Acknowledge the user's brief via the discovery question form atom.
2. Use TodoWrite to plan the brief.
3. Emit the brief as a deck artifact.

---
> Source: [nexu-io/open-design](https://github.com/nexu-io/open-design) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-26 -->
