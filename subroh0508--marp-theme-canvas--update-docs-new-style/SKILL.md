---
name: update-docs-new-style
description: Detect newly added SCSS files and create/update corresponding documentation (demo, CLAUDE.md, README) Use when this capability is needed.
metadata:
  author: subroh0508
---

# update-docs-new-style

Detect newly added SCSS files in `scss/canvas/` and create/update all related documentation.

## Steps

Execute each step file in order. After completing each step, commit and push the changes.

1. Read and execute `.claude/skills/update-docs-new-style/step/01-detect-changes.md`
2. Read and execute `.claude/skills/update-docs-new-style/step/02-create-demo.md`
3. Read and execute `.claude/skills/update-docs-new-style/step/03-create-docs-page.md`
4. Read and execute `.claude/skills/update-docs-new-style/step/04-update-claude-md.md`
5. Read and execute `.claude/skills/update-docs-new-style/step/05-update-demo-readme.md`
6. Read and execute `.claude/skills/update-docs-new-style/step/06-update-readme.md`

## Commit Convention

Each step's commit message should follow this format:

```
docs: <description of changes>

Co-Authored-By: Claude <noreply@anthropic.com>
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/subroh0508) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
