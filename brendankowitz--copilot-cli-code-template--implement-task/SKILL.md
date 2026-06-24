---
name: implement-task
description: > Use when this capability is needed.
metadata:
  author: brendankowitz
---

# Implement and Iterate Task

Implement tasks using appropriate coding agents with continuous build verification.

**Usage**: When user provides a task to implement

## Instructions

- Respect AGENTS.md (and Claude.md if it exists)
- Use MCP servers to assist
- Delegate to appropriate coding agents when possible:
  - **Fast Coding Agent**: Simple tasks, single-file edits
  - **Coding Agent**: Medium complexity, multi-file changes
  - **Complex Coding Agent**: High-complexity architectural work
- Spawn as many agents as needed, including using the fleet skill for parallel work
- Always use modern language syntax when possible

## Iteration Loop

1. **Implement** sub-task
2. **Build & Test**
3. **Fix** if needed (repeat 1-2)
4. **Code review** Use multiple high-end models (Opus, Gemini Pro, Codex) to review code for quality, security, and best practices and alignment to the task. Iterate and fix (critical, high, medium) feedback.
5. **Next** sub-task

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/brendankowitz) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
