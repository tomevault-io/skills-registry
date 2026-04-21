---
name: create-task-plan
description: REQUIREMENT. Use this skill FIRST when starting ANY new feature, bug fix, or refactoring task. It creates a structured status file in `docs/status/` to track the plan and progress. Use when this capability is needed.
metadata:
  author: ddien
---

# Create Task Plan Skill

This skill standardizes the planning and tracking process. You MUST use this skill at the beginning of any significant task (Feature, Bug Fix, Refactor).

## When to use this skill

- **Start of Task:** Immediately after understanding the User's request and before writing any code.
- **Context:** Feature implementation, Bug fixing, Refactoring, or complex research.

## How to use it

1. **Determine File Name:**
    - Format: `docs/status/<YYYY-MM-DD_<short_descriptive_name>.md`
    - Example: `docs/status/2026-01-25_refactor_sheet_tab.md`, `docs/status/fix_login_bug.md`

2. **Create Directory:**
    - Ensure `docs/status` exists. If not, create it.

3. **Create File Content:**
    - Use the template below. Fill in the "Context" based on the User's request and your analysis.

### Template

```markdown
# [Task Title] Status

**Ngày tạo:** [YYYY-MM-DD]
**Trạng thái:** Đang thực hiện (In Progress)

## Context
[Brief description of the context and goal of this task.]

## Vấn đề (Problem)
[List the problems explicitly. Number them if multiple.]
1.  
2.  

## Giải pháp (Design Solution)
[Describe the proposed technical solution or design approach.]

## Checklist Công Việc (Task List)
[Break down the work into actionable steps.]

### 1. [Phase 1 Name]
- [ ] [Task 1]
- [ ] [Task 2]

### 2. [Phase 2 Name]
- [ ] [Task 3]

## Verification Plan
- [ ] [Verification Step 1]
- [ ] [Verification Step 2]

## Nhật ký thay đổi (Changelog)
- **[YYYY-MM-DD]:** [Log item: File created / Plan updated / Task completed]
```

1. **Maintenance:**
    - Update the `Task List` status (`[x]`) as you complete items.
    - Add entries to `Changelog` for significant milestones.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ddien) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
