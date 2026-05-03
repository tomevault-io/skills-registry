---
name: document
description: Update repo-root/docs/overview.md to reflect functional and architectural changes. Use when this capability is needed.
metadata:
  author: maryrosecook
---

# Document

## Purpose

Ensure `docs/overview.md` reflects any additions, deletions, or changes to functionality and architecture.

## Workflow

1) Read repo guidance
- Read `AGENTS.md` and `~/.codex/docs/style-guide.md`.
- Read `docs/overview.md` if present.

2) Inspect current changes
- Use `git status` and `git diff` to identify relevant changes.
- Focus on functional behavior, architecture, and repo structure updates.

3) Update docs/overview.md
- Add, update, or remove entries so the overview matches the current code.
- Keep the doc terse and aligned with the required structure.
- Do not list test files in the repo structure section.

4) Write to shared context
- Append a "## Documentation" section to `factory/context.md`.
- Summarize updates and note any remaining doc gaps.

## Output format

- Short summary of documentation updates.
- File path list of what was changed.
- Any remaining doc risks or open questions.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/maryrosecook) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
