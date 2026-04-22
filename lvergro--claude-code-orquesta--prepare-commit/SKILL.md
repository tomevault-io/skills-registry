---
name: prepare-commit
description: > Use when this capability is needed.
metadata:
  author: lvergro
---

# /prepare-commit — Commit Readiness

Read `.claude/models.yml` for model routing. This skill uses model: haiku.

## preconditions (all must pass)
1. Run tests: `{stack.runtime.exec_prefix} {stack.commands.test}` → PASS
2. Check project-state.md → all tasks [x]
3. Run /validate-invariants → PASS

## generate message
1. Analyze `git diff --staged` (or unstaged changes)
2. Determine type: feat | fix | chore | docs
3. Write concise message: `type: description`

## output
- If ready: "✅ Commit ready: `type: message`"
- If not ready: "❌ Not ready: [reason]"

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/lvergro) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
