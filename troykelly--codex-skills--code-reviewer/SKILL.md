---
name: code-reviewer
description: Use when explicitly asked to run the code-reviewer subagent or when another skill requires the code-reviewer agent card.
metadata:
  author: troykelly
---

# Code Reviewer Subagent

Use the `code-reviewer` agent card to handle this specialized task.

## Run

```bash
codex-subagent code-reviewer <<'EOF'
[Provide the task context, scope, and any issue/PR numbers.]
EOF
```

## Notes

- Include concrete scope and constraints in the context block.
- Fold the subagent output back into the main workflow.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/troykelly) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-12 -->
