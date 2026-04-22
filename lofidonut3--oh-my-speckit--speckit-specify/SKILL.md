---
name: speckit-specify
description: Create or update the feature specification from a natural language feature description. Use when defining WHAT a feature should do before any design or implementation work. Use when this capability is needed.
metadata:
  author: lofidonut3
---

# Speckit Specify Command Executor

**This skill executes the official GitHub Speckit `/speckit.specify` command.**

## Execution Protocol

When this skill is invoked, you MUST:

### 1. Load the Original Command File

Read and parse `.opencode/commands/speckit.specify.md` from the current project directory.

### 2. Process OpenCode Command Syntax

The command file uses special syntax that MUST be processed before execution:

| Syntax | Action |
|--------|--------|
| `$ARGUMENTS` | Replace with user-provided arguments (the feature description) |
| `$1`, `$2`, etc. | Replace with positional arguments |
| `@filepath` | Read the file at `filepath` and insert its full contents |
| `!`command`` | Execute the shell command and insert its stdout |

### 3. Execute the Processed Instructions

After syntax processing, follow all instructions in the command file **exactly as written**, including:
- Running `.specify/scripts/powershell/create-new-feature.ps1` as specified
- Reading `.specify/templates/spec-template.md` for structure
- Creating the spec file at the designated location
- Creating quality validation checklist

### 4. Maintain Speckit Workflow Integrity

- Honor the `handoffs` defined in the command's YAML frontmatter
- Suggest `/speckit.clarify` or `/speckit.plan` as next steps
- Preserve all Speckit conventions (branch naming, file paths, structure)

## User Input

```text
$ARGUMENTS
```

## Fallback

If `.opencode/commands/speckit.specify.md` does not exist, check for:
- `.opencode/command/speckit.specify.md` (legacy path)

If no command file is found, report an error and suggest running `specify init` first.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/lofidonut3) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
