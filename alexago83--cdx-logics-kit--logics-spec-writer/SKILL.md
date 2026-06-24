---
name: logics-spec-writer
description: Create lightweight functional specs inside this repo. Use when Codex should write a structured spec document in `logics/specs/*.md` derived from a backlog item or task, with clear scope, acceptance criteria, and validation/test plan. Use when this capability is needed.
metadata:
  author: alexago83
---

# Backlog/Task → Spec

## Create a spec doc

```bash
python logics/skills/logics-spec-writer/scripts/logics_spec.py new --title "..." --from-version X.X.X
```

## Fill it from inputs

- Start from the backlog acceptance criteria and convert them into spec sections.
- Keep it concise; put implementation details in tasks, not in specs.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/alexago83) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
