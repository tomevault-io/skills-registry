---
name: codex-skillsplus-project-sync
description: Sync Karpathy-inspired project guidance from the codex_skillsplus GitHub repository into the current project. Use when Codex should bootstrap or refresh project-local guidance files such as CLAUDE.md, .cursor/rules, and project-local skill copies from the GitHub-hosted codex_skillsplus repository. Use when this capability is needed.
metadata:
  author: zhuguang-ZFG
---

# Codex Skills Plus Project Sync

Use the plugin script to copy the latest project guidance from the GitHub repository into the current project.

## Workflow

1. Identify the current project root.
2. Run `scripts/sync_from_github.py`.
3. Confirm that the following were copied or refreshed:
   - `.codex/skills/karpathy-guidelines`
   - `.codex/skills/karpathy-guidelines-zh`
   - `.cursor/rules/karpathy-guidelines.mdc`
   - `CLAUDE.md`
   - `.codex-skillsplus/EXAMPLES.md`
4. Report what changed and whether the sync was skipped due to a recent refresh.

## Notes

- The sync source is `https://github.com/zhuguang-ZFG/codex_skillsplus`.
- The default branch is `main`.
- If the project root cannot be detected safely, stop instead of writing to an uncertain path.

---
> Source: [zhuguang-ZFG/codex_skillsplus](https://github.com/zhuguang-ZFG/codex_skillsplus) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-23 -->
