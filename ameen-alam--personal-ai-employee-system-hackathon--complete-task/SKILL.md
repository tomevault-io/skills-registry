---
name: complete-task
description: This skill should be used when marking a task as completed, adding completion metadata, archiving to Done folder, and updating Dashboard. Use when the user reports a task is finished, requests archiving a completed action item, or indicates work on a task is done. Use when this capability is needed.
metadata:
  author: ameen-alam
---

# Complete Task

Mark a task as completed and archive it to the Done folder.

## Before Implementation

Gather context to ensure successful completion:

| Source | Gather |
|--------|--------|
| **Codebase** | Check AI_Employee_Vault structure (Needs_Action/, Plans/, Done/), existing task file formats |
| **Conversation** | User's task identifier (filename, partial name, or task number), completion confirmation |
| **Dashboard** | Current pending tasks list to verify task exists and get full context |

## Required Clarifications

1. **Which task to complete?** (if user provides ambiguous identifier)
   - Check Needs_Action/ and Plans/ directories
   - Present numbered list of matching tasks if multiple found

## Optional Clarifications

2. **Complete anyway?** (only if verification shows incomplete steps)
   - Ask: "This task has X remaining steps. Complete anyway?"
   - If no: abort and report remaining steps
   - If yes: proceed with completion

## Instructions

### 1. Identify Task to Complete
User will provide either:
- Full filename (e.g., `FILE_20260110_142301_invoice.md`)
- Partial identifier (e.g., "invoice task")
- Task number from Dashboard listing

Search in these locations:
- `AI_Employee_Vault/Needs_Action/` (in-progress tasks)
- `AI_Employee_Vault/Plans/` (active plans)

### 2. Verify Completion Eligibility
- Read the task file
- Check if all required steps are completed:
  - For action files: verify all suggested actions are done
  - For plans: verify all checkboxes are checked

- If not all steps are complete:
  - Ask user if they want to complete anyway
  - If no, abort and report remaining steps

### 3. Update Task File
Add completion metadata to frontmatter:
```yaml
status: completed
completed_at: [ISO timestamp]
completed_by: ai_employee
```

Add completion summary at end of file:
```markdown
## Completion Summary
- Completed: [ISO timestamp]
- Duration: [time from detection to completion]
- Outcome: [brief description of result]
```

### 4. Move to Done Folder
- Move the updated file to `AI_Employee_Vault/Done/`
- Maintain original filename for traceability

- If related plan exists in `/Plans`:
  - Move plan to `/Done` as well
  - Link them in completion notes

### 5. Update Dashboard
- Increment completion counter
- Add to "Recent Activity" section
- Remove from "Pending Actions" list
- Update "Completed This Week" stat

## Must Avoid

- **Completing without verification**: Never complete a task without checking completion eligibility
- **Moving without updating**: Never move files to Done/ without updating Dashboard
- **Losing traceability**: Always maintain original filenames for audit trail
- **Missing metadata**: Never archive without adding completion metadata to frontmatter
- **Orphaning related files**: If a plan exists, move it to Done/ along with the task file
- **Incomplete logging**: Always create log entries with all required fields

### 6. Log Completion
Append to today's log file and include:
- Task identifier
- Completion timestamp
- Brief outcome description
- Files moved

## Validation Checklist

Before marking completion as done, verify:

- [ ] Task file contains all required completion metadata (status, completed_at, completed_by)
- [ ] Original filename maintained (no renaming during move)
- [ ] File successfully moved to Done/ folder
- [ ] Dashboard updated (completion counter, recent activity, pending actions removed)
- [ ] Log entry created with all required fields
- [ ] Related files (plans) also archived if applicable
- [ ] Completion summary added to end of task file

## Completion Summary Template

Add this to the end of completed task files:

```markdown
---

## Completion Summary
- Completed: [ISO timestamp]
- Duration: [X hours/days from detection]
- Outcome: [What was accomplished]
- Related Files:
  - Original: [Inbox/filename if applicable]
  - Plan: [Plans/plan_filename.md if applicable]
  - Archive: Done/[this file]

## Performance Metrics
- Response Time: [time from detection to first action]
- Total Time: [time from detection to completion]
- Priority: [original priority level]

*Task completed by AI Employee v0.1*
```

## Success Criteria
- Task file updated with completion metadata
- File moved to Done folder
- Dashboard updated with completion
- Log entry created
- Related files also archived

## Error Handling

If task cannot be found:
```
Error: Could not find task matching "invoice_client_a"

Available tasks in Needs_Action:
1. FILE_20260110_140512_message_from_client.md
2. FILE_20260110_135021_project_proposal.md

Please specify the full filename or select a number.
```

## Related Skills
- Use `process-needs-action` to create tasks from inbox items
- Use `update-dashboard` to refresh the dashboard after completion

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ameen-alam) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
