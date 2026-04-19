---
name: writing-tickets
description: Use when you have a spec or requirements for a multi-step task, before touching code
metadata:
  author: jssee
---

# Writing Tickets

## Overview

Create implementation tickets using `tk` assuming the engineer has zero context for our codebase and questionable taste. Document everything they need: which files to touch, code snippets, testing approach, relevant docs. Break work into bite-sized tickets. DRY. YAGNI. TDD. Frequent commits.

Assume they are a skilled developer but know almost nothing about our toolset or problem domain. Assume they don't know good test design very well.

**Announce at start:** "I'm using the writing-tickets skill to create implementation tickets."

**Context:** This should be run in a dedicated worktree (created by brainstorming skill).

## Bite-Sized Ticket Granularity

**Each ticket is one logical unit (2-15 minutes):**
- "Write failing test for X" - ticket
- "Implement X to pass tests" - ticket
- "Add validation for edge case Y" - ticket

NOT: "Write test, implement, validate, refactor, commit" - too much for one ticket.

## Workflow

### 1. Create Epic Ticket

```bash
tk create "Feature: [Name]" --type=epic --priority=2
```

Add to epic body:
- **Goal:** One sentence describing what this builds
- **Architecture:** 2-3 sentences about approach
- **Tech Stack:** Key technologies/libraries
- **Acceptance Criteria:** High-level success conditions

### 2. Create Task Tickets

For each logical unit of work:

```bash
tk create "[Task description]" --type=task --priority=2
```

Then link to epic and set dependencies:

```bash
tk dep <task-id> <epic-id>        # task is child of epic
tk dep <task-id> <blocker-id>     # task blocked by another task
```

### 3. Ticket Body Structure

Use `tk edit <id>` to add details:

```markdown
# [Task Title]

**Files:**
- Create: `exact/path/to/file.py`
- Modify: `exact/path/to/existing.py:123-145`
- Test: `tests/exact/path/to/test.py`

## Steps

1. Write failing test:
   ```python
   def test_specific_behavior():
       result = function(input)
       assert result == expected
   ```

2. Run: `pytest tests/path/test.py::test_name -v`
   Expected: FAIL with "function not defined"

3. Implement:
   ```python
   def function(input):
       return expected
   ```

4. Run tests, verify pass

5. Commit: `git commit -m "feat: add specific feature"`

## Acceptance Criteria

- [ ] Test exists and passes
- [ ] Code follows project patterns
- [ ] No regressions
```

### 4. Add Notes for Context

```bash
tk add-note <id> "Relevant docs: https://..."
tk add-note <id> "Watch out for edge case X"
```

## Remember

- Exact file paths always
- Complete code in tickets (not "add validation")
- Exact commands with expected output
- Reference relevant skills with @ syntax
- DRY, YAGNI, TDD, frequent commits

## Useful Commands

```bash
tk ls                    # list all tickets
tk ls --status=open      # open tickets only
tk ready                 # tickets ready to work (deps resolved)
tk blocked               # tickets with unresolved blockers
tk dep tree <epic-id>    # visualize task hierarchy
tk show <id>             # view ticket details
```

## Execution Handoff

After creating tickets:

**"Tickets created. View with `tk dep tree <epic-id>`. Two execution options:**

**1. Subagent-Driven (this session)** - I work through `tk ready` queue, review between tickets, fast iteration

**2. Parallel Session (separate)** - Open new session, batch execution with checkpoints

**Which approach?"**

**If Subagent-Driven chosen:**
- Use `tk ready` to get next workable ticket
- `tk start <id>` before working
- `tk close <id>` when done
- Fresh subagent per ticket + code review

**If Parallel Session chosen:**
- Guide them to open new session in worktree
- Use `tk ready` workflow in new session

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jssee) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
