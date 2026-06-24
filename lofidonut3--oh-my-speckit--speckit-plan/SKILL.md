---
name: speckit-plan
description: Execute the implementation planning workflow using the plan template to generate design artifacts. Use after /speckit.clarify to create technical architecture and implementation plan. Use when this capability is needed.
metadata:
  author: lofidonut3
---

# Speckit Plan Command Executor

**This skill executes the official GitHub Speckit `/speckit.plan` command.**

## Execution Protocol

When this skill is invoked, you MUST:

### 1. Load the Original Command File

Read and parse `.opencode/commands/speckit.plan.md` from the current project directory.

### 2. Process OpenCode Command Syntax

The command file uses special syntax that MUST be processed before execution:

| Syntax | Action |
|--------|--------|
| `$ARGUMENTS` | Replace with user-provided arguments (tech stack, architecture choices) |
| `$1`, `$2`, etc. | Replace with positional arguments |
| `@filepath` | Read the file at `filepath` and insert its full contents |
| `!`command`` | Execute the shell command and insert its stdout |

### 3. Execute the Processed Instructions

After syntax processing, follow all instructions in the command file **exactly as written**, including:
- Running `.specify/scripts/powershell/setup-plan.ps1 -Json`
- Reading FEATURE_SPEC and `.specify/memory/constitution.md`
- Executing Phase 0 (research.md) and Phase 1 (data-model.md, contracts/)
- Running `.specify/scripts/powershell/update-agent-context.ps1 -AgentType opencode`
- Performing Constitution Check validations

### 4. Maintain Speckit Workflow Integrity

- Honor the `handoffs` defined in the command's YAML frontmatter
- Suggest `/speckit.tasks` as the next step when complete
- Preserve all Speckit conventions

## User Input

```text
$ARGUMENTS
```

## Fallback

If `.opencode/commands/speckit.plan.md` does not exist, check for:
- `.opencode/command/speckit.plan.md` (legacy path)

If no command file is found, report an error and suggest running `specify init` first.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/lofidonut3) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
