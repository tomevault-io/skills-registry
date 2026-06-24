---
name: verify-workstream
description: Validate workstream documentation against codebase reality. Use when this capability is needed.
metadata:
  author: fall-out-bug
---

# @verify-workstream

Before @build or @oneshot, validate docs match codebase.

## Workflow

1. **Read WS** — Parse frontmatter: goal, scope_files, acceptance_criteria
2. **Find files** — Glob to locate scope files. Gate: all must exist
3. **Read implementation** — Parse structure, identify patterns
4. **Compare** — Table: Documentation | Reality | Status
5. **Recommend** — PAUSE (high mismatch) / PROCEED / PROCEED WITH ADAPTATIONS

## Output

Verification complete. Severity. Recommendation. Comparison table.

## Integration

@build invokes this before execution.

## See Also

- @reality-check — Quick single-file check
- @build — Auto-runs verification

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/fall-out-bug) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
