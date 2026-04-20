---
name: ralph
description: Convert PRDs to prd.json format for the Ralph autonomous agent system. Use when you have an existing PRD and need to convert it to Ralph's JSON format. Triggers on: convert this prd, turn this into ralph format, create prd.json from this, ralph json. Use when this capability is needed.
metadata:
  author: hellanglez
---

# Ralph PRD Converter

Converts existing PRDs to the prd.json format that Ralph uses for autonomous execution.

---

## The Job

Take a PRD (markdown file or text) and convert it to `prd.json` in the **current project root directory** (where you would run `ralph.sh`).

**IMPORTANT:**
- Output to `./prd.json` (project root), NOT `~/.claude/skills/ralph/prd.json`
- Also create empty `./progress.txt` if it doesn't exist
- The project root is where the user is currently working (check with `pwd`)

---

## Output Format

```json
{
  "project": "[Project Name]",
  "branchName": "ralph/[feature-name-kebab-case]",
  "description": "[Feature description from PRD title/intro]",
  "userStories": [
    {
      "id": "US-001",
      "title": "[Story title]",
      "description": "As a [user], I want [feature] so that [benefit]",
      "acceptanceCriteria": [
        "Criterion 1",
        "Criterion 2",
        "Typecheck passes"
      ],
      "priority": 1,
      "passes": false,
      "notes": ""
    }
  ]
}
```

---

## Story Size: The Number One Rule

**Each story must be completable in ONE Ralph iteration (one context window).**

Ralph spawns a fresh Amp instance per iteration with no memory of previous work. If a story is too big, the LLM runs out of context before finishing and produces broken code.

### Right-sized stories:
- Add a database column and migration
- Add a UI component to an existing page
- Update a server action with new logic
- Add a filter dropdown to a list

### Too big (split these):
- "Build the entire dashboard" - Split into: schema, queries, UI components, filters
- "Add authentication" - Split into: schema, middleware, login UI, session handling
- "Refactor the API" - Split into one story per endpoint or pattern

**Rule of thumb:** If you cannot describe the change in 2-3 sentences, it is too big.

---

## Story Ordering: Dependencies First

Stories execute in priority order. Earlier stories must not depend on later ones.

**Correct order:**
1. Schema/database changes (migrations)
2. Server actions / backend logic
3. UI components that use the backend
4. Dashboard/summary views that aggregate data

**Wrong order:**
1. UI component (depends on schema that does not exist yet)
2. Schema change

---

## Acceptance Criteria: Must Be Verifiable

Each criterion must be something Ralph can CHECK, not something vague.

### Good criteria (verifiable):
- "Add `status` column to tasks table with default 'pending'"
- "Filter dropdown has options: All, Active, Completed"
- "Clicking delete shows confirmation dialog"
- "Unit tests written with >80% coverage"
- "All tests pass"
- "Typecheck passes"

### Bad criteria (vague):
- "Works correctly"
- "User can do X easily"
- "Good UX"
- "Handles edge cases"

### MANDATORY Acceptance Criteria

**For EVERY story, include these at the end:**
```
"Unit tests written for all new functions/components",
"All tests pass (npm test)",
"Test coverage >80% for new code",
"Typecheck passes",
"Lint passes with no errors"
```

**For UI stories (components, pages), also include:**
```
"Browser verification using chrome-devtools MCP - navigate to page and verify UI works"
```

**For the FINAL story in the PRD, also include:**
```
"Full test suite passes (all project tests)",
"Full E2E test with chrome-devtools MCP - test complete user flows",
"Build succeeds (npm run build)",
"Application works end-to-end"
```

### Exceptions (can skip unit tests for):
- Stories that ONLY configure environment variables or API keys
- Stories that ONLY modify config files with no logic
- For external API calls: mock the API, don't skip tests entirely

---

## Conversion Rules

1. **Each user story becomes one JSON entry**
2. **IDs**: Sequential (US-001, US-002, etc.)
3. **Priority**: Based on dependency order, then document order
4. **All stories**: `passes: false` and empty `notes`
5. **branchName**: Derive from feature name, kebab-case, prefixed with `ralph/`
6. **Always add**: "Typecheck passes" to every story's acceptance criteria

---

## Splitting Large PRDs

If a PRD has big features, split them:

**Original:**
> "Add user notification system"

**Split into:**
1. US-001: Add notifications table to database
2. US-002: Create notification service for sending notifications
3. US-003: Add notification bell icon to header
4. US-004: Create notification dropdown panel
5. US-005: Add mark-as-read functionality
6. US-006: Add notification preferences page

Each is one focused change that can be completed and verified independently.

---

## Example

**Input PRD:**
```markdown
# Task Status Feature

Add ability to mark tasks with different statuses.

## Requirements
- Toggle between pending/in-progress/done on task list
- Filter list by status
- Show status badge on each task
- Persist status in database
```

**Output prd.json:**
```json
{
  "project": "TaskApp",
  "branchName": "ralph/task-status",
  "description": "Task Status Feature - Track task progress with status indicators",
  "userStories": [
    {
      "id": "US-001",
      "title": "Add status field to tasks table",
      "description": "As a developer, I need to store task status in the database.",
      "acceptanceCriteria": [
        "Add status column: 'pending' | 'in_progress' | 'done' (default 'pending')",
        "Generate and run migration successfully",
        "Unit tests written for migration and schema",
        "All tests pass (npm test)",
        "Test coverage >80% for new code",
        "Typecheck passes",
        "Lint passes with no errors"
      ],
      "priority": 1,
      "passes": false,
      "notes": ""
    },
    {
      "id": "US-002",
      "title": "Display status badge on task cards",
      "description": "As a user, I want to see task status at a glance.",
      "acceptanceCriteria": [
        "Each task card shows colored status badge",
        "Badge colors: gray=pending, blue=in_progress, green=done",
        "Unit tests written for StatusBadge component",
        "All tests pass (npm test)",
        "Test coverage >80% for new code",
        "Typecheck passes",
        "Lint passes with no errors",
        "Browser verification using chrome-devtools MCP - navigate to task list and verify badges display correctly"
      ],
      "priority": 2,
      "passes": false,
      "notes": ""
    },
    {
      "id": "US-003",
      "title": "Add status toggle to task list rows",
      "description": "As a user, I want to change task status directly from the list.",
      "acceptanceCriteria": [
        "Each row has status dropdown or toggle",
        "Changing status saves immediately",
        "UI updates without page refresh",
        "Unit tests written for status toggle functionality",
        "All tests pass (npm test)",
        "Test coverage >80% for new code",
        "Typecheck passes",
        "Lint passes with no errors",
        "Browser verification using chrome-devtools MCP - toggle status and verify it saves"
      ],
      "priority": 3,
      "passes": false,
      "notes": ""
    },
    {
      "id": "US-004",
      "title": "Filter tasks by status (FINAL)",
      "description": "As a user, I want to filter the list to see only certain statuses.",
      "acceptanceCriteria": [
        "Filter dropdown: All | Pending | In Progress | Done",
        "Filter persists in URL params",
        "Unit tests written for filter functionality",
        "All tests pass (npm test)",
        "Test coverage >80% for new code",
        "Typecheck passes",
        "Lint passes with no errors",
        "Browser verification using chrome-devtools MCP - test filter dropdown",
        "Full test suite passes (all project tests)",
        "Full E2E test with chrome-devtools MCP - test complete task status flow: create task, change status, filter by status",
        "Build succeeds (npm run build)",
        "Application works end-to-end"
      ],
      "priority": 4,
      "passes": false,
      "notes": ""
    }
  ]
}
```

---

## Archiving Previous Runs

**Before writing a new prd.json, check if there is an existing one from a different feature:**

1. Read the current `prd.json` if it exists
2. Check if `branchName` differs from the new feature's branch name
3. If different AND `progress.txt` has content beyond the header:
   - Create archive folder: `archive/YYYY-MM-DD-feature-name/`
   - Copy current `prd.json` and `progress.txt` to archive
   - Reset `progress.txt` with fresh header

**The ralph.sh script handles this automatically** when you run it, but if you are manually updating prd.json between runs, archive first.

---

## Checklist Before Saving

Before writing prd.json, verify:

- [ ] **Previous run archived** (if prd.json exists with different branchName, archive it first)
- [ ] Each story is completable in one iteration (small enough)
- [ ] Stories are ordered by dependency (schema → backend → UI)
- [ ] **EVERY story has these acceptance criteria:**
  - "Unit tests written for [specific functionality]"
  - "All tests pass (npm test)"
  - "Test coverage >80% for new code"
  - "Typecheck passes"
  - "Lint passes with no errors"
- [ ] **UI stories also have:** "Browser verification using chrome-devtools MCP - [specific action]"
- [ ] **FINAL story also has:**
  - "Full test suite passes (all project tests)"
  - "Full E2E test with chrome-devtools MCP - [test complete user flow]"
  - "Build succeeds (npm run build)"
  - "Application works end-to-end"
- [ ] Final story title includes "(FINAL)" suffix
- [ ] Acceptance criteria are verifiable (not vague)
- [ ] No story depends on a later story

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hellanglez) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
