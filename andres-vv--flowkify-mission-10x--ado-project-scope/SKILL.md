---
name: ado-project-scope
description: When using Azure DevOps MCP (user-ado) tools, always use the configured ADO project and team unless the user explicitly asks for another project. Use when calling ADO tools, work items, repos, backlogs, queries, or when the user mentions Azure DevOps or ADO in this project. Use when this capability is needed.
metadata:
  author: andres-vv
---

<!-- ================================================================
     PROJECT CONFIGURATION — update these values for your project
     ================================================================ -->

## Configuration

| Variable | Value |
|----------|-------|
| `ADO_PROJECT` | |
| `ADO_TEAM` | |

<!-- ================================================================ -->

# Azure DevOps MCP — project scope

## Scope

When using the **user-ado** (Azure DevOps) MCP server in this project, always restrict operations to `{ADO_PROJECT}`.

## Default values

| Parameter | Value |
|-----------|--------|
| **project** | `{ADO_PROJECT}` |
| **team**   | `{ADO_TEAM}` |

## Instructions

1. **Every ADO tool call**: Pass `project: "{ADO_PROJECT}"` when the tool accepts a `project` parameter.
2. **Tools that need a team** (e.g. `wit_list_backlog_work_items`, `wit_list_backlogs`, `wit_get_work_items_for_iteration`, `core_list_project_teams`): Also pass `team: "{ADO_TEAM}"` when the tool accepts a `team` parameter.
3. **Search/filter tools** (e.g. `search_workitem`): Use `project: ["{ADO_PROJECT}"]` in the project filter.
4. **Override**: Use a different project or team only when the user explicitly asks (e.g. "list issues in project X" or "show me the OtherProject board").
5. **User stories — ticket mention syntax**: In this project, user stories are referenced with the syntax **AB#[ticket_number]** (e.g. AB#1183, AB#1020), not "US" or "AB-". When referencing user stories in PR titles, commit messages, or descriptions, use this format (e.g. "AB#1020" or "feat(AB#1020): …").
6. **Creating work items**: When creating a new ticket/work item:
   - Always create a **User Story** unless the user explicitly specifies a different work item type (e.g. Bug, Task, Epic).
   - Assign it to the current sprint based on today's date. Use `wit_list_team_iterations` to find the active iteration whose date range includes the current date, then set the `System.IterationPath` field accordingly.

## Examples

- "List work items" → call with `project: "{ADO_PROJECT}"`.
- "List repos" → use project `{ADO_PROJECT}`.
- "Run query X" → pass `project: "{ADO_PROJECT}"`.
- "List issues in Contoso" → use project "Contoso" (user override).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/andres-vv) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
