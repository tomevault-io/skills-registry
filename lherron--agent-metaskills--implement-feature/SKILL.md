---
name: implement-feature
description: Implement a feature from a wrkq task. Use when given a task ID to fetch requirements, implement the feature, and document the implementation. Use when this capability is needed.
metadata:
  author: lherron
---

# Implement Feature

Fetch a wrkq task, implement the described feature, and document the implementation.

## Usage

```
/implement-feature <task-id>
```

## Process

### Step 1: Fetch Task Details

Read the task by ID:

```bash
wrkq cat <task-id>
```

### Step 2: Understand Requirements

Before implementing:
1. Read the task description thoroughly
2. Explore any referenced files or code
3. Understand the scope and constraints
4. Identify acceptance criteria or success conditions
5. Note any shaped specifications or prior comments

### Step 3: Plan Implementation

Create a todo list with the implementation steps:
1. Break the feature into discrete, testable units
2. Identify files to create or modify
3. Plan test coverage
4. Note any dependencies or prerequisites

### Step 4: Implement the Feature

Execute the implementation plan:
1. Write code following existing patterns in the codebase
2. Add appropriate tests
3. Run tests and fix any failures
4. Run e2e integration tests
5. Run linting/type checking if configured

### Step 5: Verify Completion

Ensure the implementation meets requirements:
- All tests pass
- Code follows project conventions
- Feature works as specified
- No regressions introduced

### Step 6: Document Implementation

Add a comment to the task with implementation details:

```bash
wrkq comment <task-id> "Implementation complete:

**Changes:**
- <list of files changed/created>

**Summary:**
<brief description of what was implemented>

**Testing:**
<how the implementation was verified>

**Notes:**
<any relevant notes, edge cases, or follow-up items>"
```

### Step 7: Update Task State

Mark the task as completed:

```bash
wrkq set <task-id> --state completed
```

## Example

```
User: /implement-feature T-00042

Claude: Let me fetch the task details...

[Runs: wrkq cat T-00042]

**T-00042: Add user preferences API**
- Priority: 2
- State: open
- Description: REST API for user preferences with GET/PUT endpoints...

I'll implement this feature. Creating a todo list:

1. Define preferences schema/types
2. Add database migration
3. Implement GET /api/preferences endpoint
4. Implement PUT /api/preferences endpoint
5. Add validation
6. Write integration tests
7. Run tests and verify

[Implements feature...]

Implementation complete. Let me add a comment and close the task.

[Runs: wrkq comment T-00042 "Implementation complete:

**Changes:**
- src/types/preferences.ts (new)
- src/api/preferences.ts (new)
- migrations/003_preferences.sql (new)
- tests/api/preferences.test.ts (new)

**Summary:**
Added REST API for user preferences with GET/PUT endpoints, JSON schema validation, and per-user storage.

**Testing:**
- 8 integration tests passing
- Manual verification of both endpoints

**Notes:**
- Preferences are stored in PostgreSQL
- Schema supports theme, notifications, locale fields"]

[Runs: wrkq set T-00042 --state done]

Task T-00042 implemented and closed.
```

## Non-Interactive Mode
This skill runs in non-interactive mode. The user cannot respond to questions during execution.

**Implications:**
- Do NOT use AskUserQuestion or wait for user input
- Make reasonable decisions autonomously based on task context
- If requirements are ambiguous, use your best judgment and document assumptions in the comment
- If the task is too underspecified to implement safely, add a comment explaining what's missing and set state to `blocked` instead of `done`

## Notes

- This skill implements the feature, not just shapes it
- Always run tests before marking complete
- Document changes thoroughly in the comment
- If truly blocked, document why and set state to `blocked`
- If the task is underspecified, document assumptions made during implementation

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/lherron) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
