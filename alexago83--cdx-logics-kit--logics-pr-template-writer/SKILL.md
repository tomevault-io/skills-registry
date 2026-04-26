---
name: logics-pr-template-writer
description: Generate a pull request description from a Logics task. Use when Codex should read `logics/tasks/*.md` and produce a PR template (summary, scope, validation, risks) suitable for GitHub/GitLab. Use when this capability is needed.
metadata:
  author: alexago83
---

# PR template

## Generate from a task

```bash
python logics/skills/logics-pr-template-writer/scripts/generate_pr_template.py logics/tasks/task_000_example.md --out PR.md
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/alexago83) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
