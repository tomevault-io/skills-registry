---
name: speckit-implement
description: Execute the implementation plan by processing and executing all tasks defined in tasks.md. Use after /speckit.tasks to actually build the feature following TDD approach. Use when this capability is needed.
metadata:
  author: lofidonut3
---

# Speckit Implement Command Executor

**This skill executes the official GitHub Speckit `/speckit.implement` command.**

## Execution Protocol

When this skill is invoked, you MUST:

### 1. Load the Original Command File

Read and parse `.opencode/commands/speckit.implement.md` from the current project directory.

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
- Checking checklists status and prompting user if incomplete
- Loading tasks.md, plan.md, and other design documents
- Executing tasks phase-by-phase with TDD approach
- Marking completed tasks as [X] in tasks.md

### 4. Maintain Speckit Workflow Integrity

- Follow TDD: tests before implementation
- Report progress after each task
- Halt on failures, continue parallel tasks
- Verify all tasks completed at end

## User Input

```text
$ARGUMENTS
```

## Fallback

If `.opencode/commands/speckit.implement.md` does not exist, check for:
- `.opencode/command/speckit.implement.md` (legacy path)

If no command file is found, report an error and suggest running `specify init` first.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/lofidonut3) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
