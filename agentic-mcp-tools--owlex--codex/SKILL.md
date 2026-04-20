---
name: codex
description: Start an OpenAI Codex CLI session for deep reasoning and code analysis Use when this capability is needed.
metadata:
  author: agentic-mcp-tools
---

# Codex Session

Start a Codex CLI session for deep reasoning, code review, and analysis.

## Instructions

1. Take the user's prompt from: $ARGUMENTS
2. If no argument provided, ask what they want Codex to help with
3. Determine if new or continuation:
   - New topic: Use `start_codex_session`
   - Follow-up: Use `resume_codex_session`
4. Return immediately with the task_id
5. Tell the user:
   - "Codex started (task: <task_id>)"
   - "Check results with: `/codex-result <task_id>`"

**Do NOT call wait_for_task. Return immediately.**

## Usage

- `/codex Explain how the auth flow works`
- `/codex Find bugs in this function`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/agentic-mcp-tools) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
