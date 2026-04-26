---
name: planning-projects
description: Use when you have a spec or requirements for a multi-step task, before touching code. Writes comprehensive implementation plans.
metadata:
  author: valec3
---

# Planning Projects

## When to use this skill
- When starting a new feature implementation or complex task.
- When you have a clear spec or requirements but haven't written code yet.
- When the user asks for a plan or implementation strategy.

## Workflow

1.  **Analyze Context**: Understand the goal, architecture, and tech stack.
2.  **Initialize Plan Document**: Create a file at `docs/plans/YYYY-MM-DD-<feature-name>.md`.
3.  **Break Down Tasks**: Create bite-sized, atomic tasks (2-5 minutes each).
4.  **Detail Steps**: For each task, define files to create/modify, tests to write, and exact code changes.
5.  **Review**: Ensure TDD, DRY, and YAGNI principles are followed.

## Instructions

**Goal**: Write comprehensive implementation plans assuming the implementer has zero context.

### Plan Structure
Every plan **MUST** start with this header:

```markdown
# [Feature Name] Implementation Plan

**Goal:** [One sentence describing what this builds]

**Architecture:** [2-3 sentences about approach]

**Tech Stack:** [Key technologies/libraries]

---
```

### Task Granularity
Each step should be one atomic action:
- "Write the failing test"
- "Run it to make sure it fails"
- "Implement the minimal code to make the test pass"
- "Run the tests and make sure they pass"
- "Commit"

### Content Requirements
- **Exact File Paths**: Always use full paths.
- **Complete Code**: Provide actual code, not descriptions like "add validation".
- **Commands**: List specific commands to run tests or builds.

## Resources
- [No specific resources, relies on project context]

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/valec3) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
