---
name: code-phase
description: Implement all remaining tasks in the current phase without stopping Use when this capability is needed.
metadata:
  author: jerichobob
---

# Implement Next Phase

Implement all remaining tasks in the current phase without stopping between tasks.

## Pre-parsed Data

!`${CLAUDE_PLUGIN_ROOT}/scripts/specs-parse.sh next-phase`

## Instructions

1. If the data above shows `NO_TASKS_REMAINING`, report that all specs are complete and suggest creating a new spec with `/sdd:spec`.

2. **Read the spec file** shown in `spec_file` above for full implementation context.

3. **Create a todo list**: Use TodoWrite to create tasks for ALL unchecked items (`[ ]` lines) in the phase data above.

4. **Implement each task sequentially**:
   - Mark each task as `in_progress` when starting
   - Implement the task fully
   - Update `specs/README.md` to mark the task as complete (`- [x]`)
   - Mark the todo as `completed`
   - Move immediately to the next task

5. **Do NOT stop between tasks**: Continue implementing until ALL tasks in the phase are complete.

6. **After completing the phase**:
   - Run any relevant tests if they exist
   - Bump the version in `app.py` if code was changed (patch for fixes, minor for features)
   - Report a summary of what was implemented

## Important

- **Do not ask for confirmation between tasks** — implement the entire phase
- **Update specs/README.md after each task** — keep the checklist in sync
- **Read the full spec file** for additional context
- **Follow existing code patterns** in the codebase
- **Test as you go** when practical

## Output Format

When starting:

```text
🚀 Implementing Phase: {Phase Name}
Spec: v{N} - {Name}
Tasks to complete: {count}

Starting implementation...
```

When complete:

```text
✅ Phase Complete: {Phase Name}

Implemented:
- {Task 1}
- {Task 2}
- {Task 3}

Version bumped: {old} → {new}
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jerichobob) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
