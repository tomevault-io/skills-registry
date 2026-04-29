---
name: validate
description: Validate checklist items for a ClickUp task without changing status Use when this capability is needed.
metadata:
  author: thebushidocollective
---

# validate

## Name

clickup:validate - Validate checklist items for a ClickUp task without changing status

## Synopsis

```
/validate [arguments]
```

## Description

Validate checklist items for a ClickUp task without changing status

## Implementation

Validate that all checklist items are complete for a ClickUp task without transitioning status.

**Usage**: `/validate #ABC123` or `/validate ABC123`

Use `clickup_get_task` to fetch task details and all checklist items.

**Display Format**:

```
🔍 Validating #ABC123: {name}

Current Status: {status}
Assignees: {assignees}

📋 Checklists:

Checklist: "Acceptance Criteria"
1. ✓ {item 1}
   Evidence: {ask user or check recent comments/code changes}

2. ✓ {item 2}
   Evidence: {ask user or check recent comments/code changes}

3. ✗ {item 3}
   Status: Not complete

4. ✓ {item 4}
   Evidence: {ask user or check recent comments/code changes}

Checklist: "Testing"
1. ✓ {item 1}
2. ✓ {item 2}

Summary: 5/6 items complete (83%)

Remaining work:
- {item 3}: {suggest what needs to be done}

Ready to complete? No - complete remaining checklist items first.
```

Provide actionable feedback on what still needs to be done.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/thebushidocollective) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
