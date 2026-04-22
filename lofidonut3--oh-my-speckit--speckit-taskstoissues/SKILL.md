---
name: speckit-taskstoissues
description: Convert existing tasks into actionable, dependency-ordered GitHub issues for the feature based on available design artifacts. Use when you want to create GitHub issues from your tasks.md. Use when this capability is needed.
metadata:
  author: lofidonut3
---

# Speckit Tasks To Issues Command Executor

**This skill executes the official GitHub Speckit `/speckit.taskstoissues` command.**

## Execution Protocol

When this skill is invoked, you MUST:

### 1. Load the Original Command File

Read and parse `.opencode/commands/speckit.taskstoissues.md` from the current project directory.

### 2. Process OpenCode Command Syntax

The command file uses special syntax that MUST be processed before execution:

| Syntax | Action |
|--------|--------|
| `$ARGUMENTS` | Replace with user-provided arguments |
| `$1`, `$2`, etc. | Replace with positional arguments |
| `@filepath` | Read the file at `filepath` and insert its full contents |
| `!`command`` | Execute the shell command and insert its stdout |

### 3. Execute the Processed Instructions

After syntax processing, follow all instructions in the command file **exactly as written**, including:
- Running `.specify/scripts/powershell/check-prerequisites.ps1 -Json -RequireTasks -IncludeTasks`
- Extracting path to tasks.md
- Running `git config --get remote.origin.url` to verify GitHub remote
- Creating GitHub issues for each task using GitHub MCP server

### 4. Critical Safety Rules

- **ONLY proceed if remote is a GitHub URL**
- **NEVER create issues in repositories that don't match the remote URL**

## User Input

```text
$ARGUMENTS
```

## Fallback

If `.opencode/commands/speckit.taskstoissues.md` does not exist, check for:
- `.opencode/command/speckit.taskstoissues.md` (legacy path)

If no command file is found, report an error and suggest running `specify init` first.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/lofidonut3) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
