---
name: type-design-analyzer
description: Use when asked to evaluate type design/invariants or explicitly asked to run the type-design-analyzer subagent.
metadata:
  author: troykelly
---

# Type Design Analyzer Subagent

Use the `type-design-analyzer` agent card to handle this specialized task.

## Run

```bash
codex-subagent type-design-analyzer <<'EOF'
[Provide the task context, scope, and any issue/PR numbers.]
EOF
```

## Notes

- Include concrete scope and constraints in the context block.
- Fold the subagent output back into the main workflow.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/troykelly) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-12 -->
