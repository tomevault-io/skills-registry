---
name: generate-and-execute
description: Generate a detailed prompt for Claude Code by analyzing the codebase, then execute it immediately after user confirmation Use when this capability is needed.
metadata:
  author: jason-hchsieh
---

# Generate and Execute Prompt

You are generating a comprehensive prompt and then executing it directly. This is the same as `/generate-prompt` but instead of just displaying the prompt, you execute the task after user confirmation.

## Workflow

### 1. Gather User Intent

If the user didn't provide a task description with `/generate-and-execute`, ask:

> What do you want Claude Code to do? Describe the task, feature, bug fix, or change you need.

### 2. Analyze the Codebase

Gather context that Claude Code would need. Run these in parallel:

**Project type and structure:**
- Check for `package.json`, `Cargo.toml`, `pyproject.toml`, `go.mod`, etc. to identify the tech stack
- Read `CLAUDE.md` if it exists (contains project-specific instructions)
- Read `README.md` for project overview
- Run `ls` on the root to understand top-level structure

**Relevant files:**
- Based on the user's task, identify which files and directories are most relevant
- Use Grep to find related code (function names, component names, API endpoints mentioned in the task)
- Read key files to understand patterns, conventions, and architecture

**Dependencies and tools:**
- Check dependency files for relevant libraries
- Check for config files (`.eslintrc`, `tsconfig.json`, `Makefile`, etc.)
- Check for test framework setup

**Git context:**
- Current branch: `git branch --show-current`
- Recent commits: `git log --oneline -5`
- Any uncommitted changes: `git status --short`

### 3. Generate the Prompt

Structure the prompt with these sections:

```markdown
# Task
[Clear, actionable description]

## Context
[Project info, key files, branch]

## Codebase Overview
[Architecture summary relevant to the task]

## Relevant Code
[Snippets and file references]

## Requirements
[Specific requirements and acceptance criteria]

## Implementation Hints
[Suggested approach, files to modify, patterns to follow]
```

### 4. Show and Confirm

Display the generated prompt and ask for confirmation:

```
Here's the prompt I'll execute:

---
[the full prompt]
---

Proceed with execution?
```

**Wait for explicit user confirmation before proceeding.** Do not auto-execute.

### 5. Execute

After confirmation, follow the prompt instructions yourself:
- Read the files identified in the prompt
- Implement the changes following the approach outlined
- Write tests if the prompt calls for them
- Follow the conventions and patterns identified

Report progress as you work through the task.

### 6. Offer to Save

After execution, ask if the user wants to save the prompt for reuse:
- Save to `.claude/prompts/<task-slug>.md`

## Arguments

| Argument | Effect |
|----------|--------|
| (none) | Interactive — ask what the user wants |
| `<task description>` | Use the provided description directly |
| `--save` | Auto-save the prompt to `.claude/prompts/` |

## Important

- **Always show the prompt and ask for confirmation** before executing
- The prompt serves as both documentation and instruction
- If the user says "no" to execution, just display the prompt (same as `/generate-prompt`)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jason-hchsieh) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
