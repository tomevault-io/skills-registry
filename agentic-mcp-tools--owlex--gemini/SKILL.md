---
name: gemini
description: Start a Google Gemini CLI session with 1M context for large codebase analysis Use when this capability is needed.
metadata:
  author: agentic-mcp-tools
---

# Gemini Session

Start a Gemini CLI session with 1M context window for large codebase analysis.

## Instructions

1. Take the user's prompt from: $ARGUMENTS
2. If no argument provided, ask what they want Gemini to help with
3. Determine if new or continuation:
   - New topic: Use `start_gemini_session`
   - Follow-up: Use `resume_gemini_session`
4. Return immediately with the task_id
5. Tell the user:
   - "Gemini started (task: <task_id>)"
   - "Check results with: `/gemini-result <task_id>`"

**Do NOT call wait_for_task. Return immediately.**

## Usage

- `/gemini Analyze the entire codebase structure`
- `/gemini Summarize all API endpoints`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/agentic-mcp-tools) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
