---
name: comment-analyzer
description: Use when asked to review code comments for accuracy/quality or explicitly asked to run the comment-analyzer subagent.
metadata:
  author: troykelly
---

# Comment Analyzer Subagent

Use the `comment-analyzer` agent card to handle this specialized task.

## Run

```bash
codex-subagent comment-analyzer <<'EOF'
[Provide the task context, scope, and any issue/PR numbers.]
EOF
```

## Notes

- Include concrete scope and constraints in the context block.
- Fold the subagent output back into the main workflow.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/troykelly) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-12 -->
