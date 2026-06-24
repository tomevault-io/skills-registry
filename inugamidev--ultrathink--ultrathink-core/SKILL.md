---
name: ultrathink-core
description: UltraThink operating layer for Codex: load AGENTS.md, use VFS before broad reads, route to the full .claude/skills mesh on demand, preserve privacy, memory, and handoff rules. Use when this capability is needed.
metadata:
  author: InugamiDev
---

# UltraThink Core

Use this skill for non-trivial work in this repository or any project where UltraThink is installed.

## Contract

1. Read `AGENTS.md` for the active Codex instructions.
2. Check `.ckignore` before broad file exploration.
3. Use VFS/code-intel before full file reads when those tools are available.
4. Route to the full skill mesh only on demand:
   - registry: `.claude/skills/_registry.json`
   - skill bodies: `.claude/skills/<name>/SKILL.md`
5. Keep user/developer/system instructions above UltraThink when there is a conflict.
6. For substantial multi-step work, create or update a Markdown task ledger and mark tasks complete as they land. See `docs/protocols/task-ledger.md`.
7. Preserve unrelated worktree changes.
8. Before ending substantial work, write a `.handoff/handoff-<DD-mon-YYYY>-<HHMM>.md` file.

## Why This Skill Is Small

Codex has a startup skill-description budget. Do not expose every UltraThink skill directly in `.agents/skills`; that causes budget warnings and omitted skills. This facade keeps Codex startup small while preserving on-demand access to the complete `.claude/skills` mesh.

---
> Source: [InugamiDev/ultrathink](https://github.com/InugamiDev/ultrathink) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
