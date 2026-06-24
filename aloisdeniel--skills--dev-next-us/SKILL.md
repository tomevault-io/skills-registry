---
name: dev-next-us
description: Implement the next highest priority user story from the PRD. Triggers on: implement the next feature, dev next feature. Use when this capability is needed.
metadata:
  author: aloisdeniel
---

# Implementing the next User Story

You are an autonomous coding agent working on a software project who implement the next user story in the provided prd file.

## Your Task

1. Use `prd` skill to make sure there is an active feature before starting.
2. Use `prd` skill to find the next user story to implement.
3. Use `prd` skill to read both feature requirements, and list progress notes. 
4. Analyze the screenshots (if any) for inspecting the overall layout idea.
5. Implement that single user story
6. Run quality checks (e.g., typecheck, lint, test - use whatever your project requires)
7. Update AGENTS.md files if you discover reusable patterns (see below)
8. Ask the user to review the current changes.
9. Use `prd` skill to add a `note` with your progress report including learnings for future iterations.
9. If checks pass, mark the feature as complete with the `prd` skill.

## Progress Note Report Format

```
- What was implemented
- Files changed
- **Learnings for future iterations:**
  - Patterns discovered (e.g., "this codebase uses X for Y")
  - Gotchas encountered (e.g., "don't forget to update Z when changing W")
  - Useful context (e.g., "the evaluation panel is in component X")
---
```

The learnings section is critical - it helps future iterations avoid repeating mistakes and understand the codebase better.

## Update AGENTS.md Files

Before committing, check if any edited files have learnings worth preserving in nearby AGENTS.md files:

1. **Identify directories with edited files** - Look at which directories you modified
2. **Check for existing AGENTS.md** - Look for AGENTS.md in those directories or parent directories
3. **Add valuable learnings** - If you discovered something future developers/agents should know:
   - API patterns or conventions specific to that module
   - Gotchas or non-obvious requirements
   - Dependencies between files
   - Testing approaches for that area
   - Configuration or environment requirements

**Examples of good AGENTS.md additions:**
- "When modifying X, also update Y to keep them in sync"
- "This module uses pattern Z for all API calls"
- "Tests require the dev server running on PORT 3000"
- "Field names must match the template exactly"

**Do NOT add:**
- Story-specific implementation details
- Temporary notes

Only update AGENTS.md if you have **genuinely reusable knowledge** that would help future work in that directory.

## Quality Requirements

- ALL completions must pass your project's quality checks (typecheck, lint, test)
- Do NOT complete with broken code
- Keep changes focused and minimal
- Follow existing code patterns

## Important

- Work on ONE story
- Ask user for review before marking the story complete

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/aloisdeniel) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
