---
name: gipleis-commit-pr
description: Generates commit messages and PR descriptions in GipleisDev format. Use when the user asks for a commit message, PR description, merge checklist, or how to format commits/PRs.
metadata:
  author: filipesalvio-code
---

# Gipleis commit & PR format

## When to use

- User asks for a commit message, PR description, or merge checklist.
- User asks how to format commits or PRs in this project.

## Commit message

```
ralph: [ID] [action] - brief description
```

- **ID:** Task or criterion (e.g. `TASK-42`, `criterion-3`).
- **action:** Verb (add, fix, refactor, docs, etc.).
- **brief description:** One line; no period at end.

**Examples:**
- `ralph: [TASK-42] add retry - night shift timeout handling`
- `ralph: criterion-3 fix - docstrings in jules_api`

One logical change per commit. Commit after each breakthrough.

## PR title

```
feat(agent): [Agent ID] - [Action]
```

Or conventional: `feat(...)`, `fix(...)`, `docs(...)`, etc.

## PR body (required sections)

- **Motivation** – Why this change.
- **Changes** – What was done (list or short paragraphs).
- **Verification** – How it was tested or validated (e.g. pytest, manual steps).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/filipesalvio-code) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
