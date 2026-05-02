---
name: codex-openclaw
description: Drive OpenAI Codex CLI from OpenClaw sessions for real coding work (implement, refactor, test, review). Use when user asks to code with Codex, run iterative coding tasks in a repo, or orchestrate Codex runs with logs and progress updates. Use when this capability is needed.
metadata:
  author: sandie-ai
---

# Codex + OpenClaw

Use Codex CLI as the coding engine from OpenClaw.

## Prerequisites

- `codex` available in PATH
- authenticated Codex CLI session
- run inside a git repo
- when invoked through OpenClaw tools, prefer PTY execution

## Quick checks

```bash
codex --version
git rev-parse --is-inside-work-tree
```

If not in repo:

```bash
mkdir -p /tmp/codex-scratch && cd /tmp/codex-scratch && git init
```

## Recommended execution patterns

### One-shot edit

```bash
codex exec "Implement <feature>, add tests, run checks, and summarize changes."
```

### Long task (background pattern)

```bash
codex exec "Build <feature> in small commits, run tests, then provide a concise report."
```

Use process logs to monitor progress and intervene only when needed.

## Prompt template

```text
Context: <project + goal>
Constraints:
- keep changes minimal and targeted
- preserve style and architecture
- add/update tests for behavior changes
- run relevant checks before finishing
Deliverables:
- changed files with rationale
- commands run + outcomes
- remaining risks / TODOs
```

## Quality guardrails

- Prefer iterative changes over broad rewrites.
- Never claim completion without command/test output.
- If blocked by missing env/secrets/tools, stop and report exact blocker.

## References

- `references/codex-openclaw-notes.md`
- Codex docs: https://developers.openai.com/codex
- OpenClaw docs: https://docs.openclaw.ai

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sandie-ai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
