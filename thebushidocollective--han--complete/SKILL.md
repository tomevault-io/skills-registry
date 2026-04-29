---
name: complete
description: Mark a ClickUp task as complete after validating checklist Use when this capability is needed.
metadata:
  author: thebushidocollective
---

# complete

## Name

clickup:complete - Mark a ClickUp task as complete after validating checklist

## Synopsis

```
/complete [arguments]
```

## Description

Mark a ClickUp task as complete after validating checklist

## Implementation

Complete a ClickUp task by validating all checklist items and transitioning to Done.

**Usage**: `/complete #ABC123` or `/complete ABC123`

**Steps**:

1. Use `clickup_get_task` to fetch task details including checklists and comments
2. Display all checklist items
3. Ask user to confirm each item is complete
4. If all confirmed:
   - Use `clickup_add_comment` to add completion summary
   - Use `clickup_update_task_status` to transition to "complete" or "done"
5. If any not confirmed:
   - List incomplete items
   - Keep task in current status
   - Suggest next steps

**Display Format**:

```
✅ Completing #ABC123: {name}

📋 Checklist Validation:

Checklist: "Acceptance Criteria"
1. ✓ {item 1} - COMPLETE
2. ✓ {item 2} - COMPLETE
3. ✗ {item 3} - INCOMPLETE
4. ✓ {item 4} - COMPLETE

❌ Cannot complete: 1 item not checked
- {item 3}

Suggestion: Complete remaining checklist items before marking as Done.
```

Only transition to Done if ALL checklist items are validated.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/thebushidocollective) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
