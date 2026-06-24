---
name: codex-claude-loop
description: Orchestrates a dual-AI engineering loop where Claude CLI runs with `--dangerously-skip-permissions` for planning/implementation and Codex validates/reviews outputs. Use when users ask for Claude+Codex collaboration, cross-review, dual AI loop, or explicit Claude CLI execution with skip-permission mode ("claude", "dangerously-skip-permission", "dangerously-skip-permissions", "codex claude", "dual AI", "교차 검증", "claude 협업"). Use when this capability is needed.
metadata:
  author: tygwan
---

# Codex-Claude Loop

Claude CLI + Codex 협업 루프를 실행한다.

## Preconditions

- Run `--dangerously-skip-permissions` only when the user explicitly requested it.
- Prefer isolated environment: disposable branch/worktree, no production credentials.
- Default Codex verification to `--sandbox read-only`.

## Core Loop

```
Plan (Claude) -> Validate (Codex) -> Feedback ->
Implement (Claude) -> Review (Codex) ->
Fix (Claude) -> Re-validate (Codex)
```

## Quick Commands

```bash
# 1) Claude non-interactive output
claude --dangerously-skip-permissions -p "Write an implementation plan for: <task>"

# 2) Codex validation (read-only)
echo "<claude_output>" \
| codex exec -m gpt-5.2-codex --config model_reasoning_effort="high" --sandbox read-only

# 3) Claude interactive implementation session
claude --dangerously-skip-permissions
```

## Scripted Commands

Use the bundled helper script for repeatable runs:

```bash
# Claude one-shot
bash .codex/skills/codex-claude-loop/scripts/claude-codex-loop.sh claude-print "Plan this task: <task>"

# Codex one-shot review
bash .codex/skills/codex-claude-loop/scripts/claude-codex-loop.sh codex-review "Review this plan: <plan>"

# One roundtrip: Claude plan -> Codex review
bash .codex/skills/codex-claude-loop/scripts/claude-codex-loop.sh roundtrip "Implement feature X"
```

## Operating Rules

- Keep handoff explicit: include assumptions, constraints, and acceptance criteria in every prompt.
- Treat Claude output as draft input. Use Codex to challenge edge cases, regressions, and test gaps.
- If either CLI returns non-zero exit, stop and summarize the failure before retrying.
- For high-impact edits (schema, auth, deployment), ask the user before applying changes.

## Recovery

- Resume Claude session: `claude -c`
- Resume Codex session: `echo "continue" | codex exec resume --last`
- If context drifts, re-anchor with a short summary and current objective.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tygwan) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
