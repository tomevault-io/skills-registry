---
name: code-spec
description: Implement ALL remaining phases and tasks in a spec without stopping Use when this capability is needed.
metadata:
  author: jerichobob
---

# Implement Complete Spec

Implement all remaining phases and tasks in the current spec without stopping between phases or tasks.

## Pre-parsed Data

!`${CLAUDE_PLUGIN_ROOT}/scripts/specs-parse.sh status`

## Instructions

1. **Find the target spec**:
   - If `$ARGUMENTS` contains a spec version (e.g., `v3`), find that spec in the status data above
   - Otherwise, use the first spec with status `In Progress` or `Draft` from the data above
   - Read the full spec file for context (find it at `specs/spec-v{N}-*.md`)

2. **Identify all remaining work**: Read `specs/README.md` and collect ALL unchecked items across ALL phases of the target spec.

3. **Create a todo list**: Use TodoWrite to create tasks for ALL unchecked items across ALL phases, organized by phase.

4. **Implement phase by phase, task by task**:
   - Work through phases in order (Phase 1, then Phase 2, etc.)
   - Within each phase, implement each task sequentially
   - Mark each task as `in_progress` when starting
   - Implement the task fully
   - Update `specs/README.md` to mark the task as complete (`- [x]`)
   - Mark the todo as `completed`
   - Move immediately to the next task
   - When a phase is complete, move immediately to the next phase

5. **Do NOT stop between tasks or phases**: Continue implementing until ALL phases in the spec are complete.

6. **After completing the entire spec**:
   - Update the spec status to `Complete` in both `specs/README.md` and the spec file
   - Run any relevant tests if they exist
   - Bump the version (patch for fixes, minor for features)
   - Report a summary of what was implemented

## Important

- **Do not ask for confirmation between tasks or phases** — implement the entire spec end-to-end
- **Update specs/README.md after each task** — keep the checklist in sync
- **Read the full spec file** for additional context on implementation details
- **Follow existing code patterns** in the codebase
- **Test as you go** when practical
- **If a task is blocked** (missing dependency, requires external config), mark it as blocked, skip it, and continue. Report blocked tasks in the summary.

## Output Format

When starting:

```text
🚀 Implementing Spec: v{N} - {Name}
Phases to complete: {count}
Total tasks remaining: {count}

Starting Phase 1: {Phase Name}...
```

When complete:

```text
✅ Spec Complete: v{N} - {Name}

Phase 1: {Phase Name}
- {Task 1}
- {Task 2}

{Blocked (if any):}
{- {Task} — {reason}}

Version bumped: {old} -> {new}
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jerichobob) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
