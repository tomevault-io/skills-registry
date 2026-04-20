---
name: manager
description: Provides a framework for task management, feature tracking, and progress reporting, mirroring GitHub Issue workflows.
metadata:
  author: rgupta-code
---

# Manager Skill

This skill allows the agent to act as a Project Manager, ensuring that every feature request is tracked, prioritized, and updated as work progresses.

## Task Tracking Mechanism

The primary source of truth for project management is the **GitHub Project (V2)**:
[Antigravity Project #4](https://github.com/users/rgupta-code/projects/4)

### Tool Interaction Policy
Project management and repository automation MUST prioritize tools in this order:
1.  **MCP Tools**: (e.g., `github.create_issue`, `github.update_project_v2_item_field_value`).
2.  **GitHub CLI**: (`gh api graphql`) only if MCP tools are unavailable or for bulk operations.
3.  **Local Mirror**: (`TASKS.md`) for immediate session context.

### Workflow
1. **New Feature Request**: Record the request in the `TASKS.md` backlog.
2. **Sync to GitHub**: 
   - Use `github.add_project_v2_item_by_id` to add tasks to Project #4.
3. **Internal Updates**: Move status to `[In Progress]` locally in `TASKS.md`.
4. **Status Sync**: 
   - Use `github.update_project_v2_item_field_value` to move cards between columns.
5. **Report**: Confirm tool execution success to the user.

## Task Structure in `TASKS.md`

Tasks must follow this format:
- `[Status] #ID: Task Title`
- **Description**: Brief objective.
- **Sub-tasks**: Checklist of implementation steps.
- **Linked Commits**: Hashes of code related to this task.

## Status Definitions

- **Backlog**: Requested but not yet started.
- **In Progress**: Currently being developed or tested.
- **Blocked**: Waiting on external input or another task.
- **Done**: Code pushed, tests passed, and documentation completed.

## Usage Instructions

When acting as the Manager:
1. **Initialize**: If `TASKS.md` doesn't exist, create it from the template in `resources/template_tasks.md`.
2. **Audit tasks**: At the start of every session, read `TASKS.md` to understand context.
3. **Auto-Update**: As soon as a file is modified that relates to a task, update the `TASKS.md` file to reflect "In Progress" or "Done".
4. **Report**: In the final message of a task, provide a summary of which tasks were updated.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rgupta-code) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
