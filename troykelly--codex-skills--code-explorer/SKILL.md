---
name: code-explorer
description: Use when asked to trace existing codepaths or explicitly asked to run the code-explorer subagent.
metadata:
  author: troykelly
---

# Code Explorer Subagent

Use the `code-explorer` agent card to handle this specialized task.

## Run

```bash
codex-subagent code-explorer <<'EOF'
[Provide the task context, scope, and any issue/PR numbers.]
EOF
```

## Notes

- Include concrete scope and constraints in the context block.
- Fold the subagent output back into the main workflow.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/troykelly) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-12 -->
