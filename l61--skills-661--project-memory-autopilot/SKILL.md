---
name: project-memory-autopilot
description: Build and maintain a project-scoped external memory system for AI collaboration. Use when users ask to make the assistant "smarter", persistent across sessions, less forgetful, or to codify team preferences, workflow rules, and decision history in repository files. Use when this capability is needed.
metadata:
  author: l61
---

# Project Memory Autopilot

Implement external memory as first-class project infrastructure, not ad-hoc notes.

## Workflow

1. Determine runtime target.
   - `codex` -> `.codex/memory/`
   - `claude` -> `.claude/memory/`
   - `opencode` -> `.opencode/memory/`
   - fallback -> `.ai/memory/`
2. Inspect existing routing/protocol files.
   - Common locations: `AGENTS.md`, `docs/guides/protocol-*.md`, local collaboration skill files.
3. Bootstrap memory storage.
   - Prefer script:
     `python scripts/bootstrap_memory.py --root <repo> --runtime <codex|claude|opencode|generic>`
   - Use `--dry-run` first, then run without it.
4. Wire memory rules into protocol docs.
   - Read memory files at non-trivial task start.
   - Define hard/soft write triggers.
   - Require final report line:
     `Memory Update: written|skipped + files + trigger`
5. Seed initial memory entries.
   - User preferences and communication constraints.
   - Active context and next priorities.
   - Decision log entry for the current change.
6. Validate before completion.
   - Memory files exist.
   - Protocol docs reference the memory path and trigger behavior.
   - Final report format includes the memory update line.

## Trigger Matrix

Read `references/memory-trigger-matrix.md` and apply:
- hard trigger: any 1 -> write memory
- soft trigger: any 2 -> write memory

## Templates

Use assets as copy-ready templates:
- `assets/user-profile.template.md`
- `assets/active-context.template.md`
- `assets/decision-log.template.jsonl`
- `assets/agents-memory-block.template.md`
- `assets/protocol-memory-block.template.md`

## Implementation Notes

- Keep memory updates minimal and append-only where possible.
- Store durable preferences in `user-profile.md`.
- Store short-lived execution state in `active-context.md`.
- Append key decisions to `decision-log.jsonl`; do not rewrite history.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/l61) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
