---
name: task-orchestration
description: Documentation-first task execution with quality checks and progress tracking. Use when working with task lists, implementing features, or executing multi-step work to ensure systematic completion with proper documentation review. Use when this capability is needed.
metadata:
  author: jpoutrin
---

# Task Orchestration Skill

This skill automatically activates when working with task lists and implementation. It enforces documentation-first development, systematic execution, and quality assurance.

## Core Principles

### Documentation-First Enforcement
**NO CODING WITHOUT DOCUMENTATION REVIEW**

The FIRST sub-task for any coding task MUST be documentation review:
1. Review relevant API/framework documentation
2. Share key findings before implementation
3. Identify best practices and anti-patterns
4. Reference documentation in implementation

### Sequential Execution Protocol

1. **One task at a time** - Only one task in_progress
2. **Complete all sub-tasks** before moving to parent
3. **User confirmation required** - Wait for "yes" or "y" before next task
4. **Immediate file updates** - Update markdown checkboxes immediately

## Automatic Behaviors

### 1. Initialize Task Tracking
- Read task list file completely
- Use TodoWrite to track all tasks
- Identify dependencies between tasks
- Verify required resources

### 2. Execute Tasks Systematically
```
1.0 Parent Task
  1.1 [FIRST] Documentation Review ← MANDATORY
  1.2 Implementation step
  1.3 Testing step
2.0 Next Parent Task
  ...
```

### 3. Update Progress Mechanically
After EACH task completion:
1. Update TodoWrite status to "completed"
2. Change `[ ]` to `[x]` in markdown file
3. Update "Relevant Files" section
4. Report completion status

### 4. Communicate Progress
```
📚 Starting task 1.1: Review documentation
   - Reviewing [framework] best practices
   - Key findings: ...

🚀 Starting task 1.2: Implementation
   - Following patterns from docs
   - Creating files...

✅ Completed task 1.2
   - File created: path/to/file.js
   - Tests passing

📋 Next: 1.3 Add tests
   Proceed? (y/n)
```

## Quality Assurance Checklist

Before marking any task complete:

### Documentation Compliance
- [ ] Documentation was reviewed FIRST
- [ ] Implementation follows documented patterns
- [ ] API/framework patterns correctly applied

### Code Quality
- [ ] Follows project conventions
- [ ] No linting errors
- [ ] Proper error handling
- [ ] Comments where necessary

### Testing
- [ ] Unit tests written and passing
- [ ] Manual testing completed
- [ ] Edge cases considered

## Error Handling Protocol

When encountering issues:

```
⚠️ Issue encountered in task 1.2:
   - Problem: [description]
   - Proposed solution: [approach]
   - Estimated impact: [time/scope]

   Proceed with solution? (y/n)
```

## New Task Discovery

When new tasks are discovered during implementation:

```
📌 New task discovered:
   - Task 1.4: [description]
   - Reason: [why this is needed]
   - Priority: [high/medium/low]

   Add to current sprint? (y/n)
```

1. Add to task list with [NEW] tag
2. Update TodoWrite
3. Get user confirmation

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jpoutrin) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
