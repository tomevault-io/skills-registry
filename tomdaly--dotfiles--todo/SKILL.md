---
name: todo
description: Find and implement the next incomplete task from the ticket file, following TDD and committing the change Use when this capability is needed.
metadata:
  author: tomdaly
---

# 📋 Find and implement the next task from the ticket file

$ARGUMENTS

## context

The task list is in `~/vault/projects/tickets/<ticketName>.md` under the `## to do` heading.

```
- [ ]  not started
- [/]  in progress
- [x]  completed
- [-]  irrelevant
```

## steps

### 1. find the next task
- Read the ticket file
- Find the next incomplete (`- [ ]`) or in-progress (`- [/]`) task
- Mark it as in-progress: `- [/]`
- Focus ONLY on this specific task — ignore all other tasks

### 2. think before coding
- Re-read the implementation plan for context
- Review changes already made in the branch (`git diff main...HEAD`)
- Identify the files that need to change
- If unsure, STOP and present questions/suggestions

### 3. implement with TDD
- **red**: write a failing test first
  - skip for frontend UI changes unless a test file already exists
  - do write tests for frontend util/lib/functionality changes
- **green**: write minimum code to pass the test
- **refactor**: clean up

### 4. validate
- Run targeted tests on changed files only
- Fix any failures before proceeding

### 5. commit
- Follow the instructions in `~/.claude/instructions/git-commits.md`
- Stage only files relevant to this task
- One commit per task (or logical chunk)

### 6. update ticket
- Mark the task as complete: `- [x]`
- If the task became irrelevant during implementation: `- [-]`

### 7. report
- Briefly state what was done
- Note any concerns or follow-up items discovered
- Ask if the user wants to continue to the next task

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tomdaly) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
