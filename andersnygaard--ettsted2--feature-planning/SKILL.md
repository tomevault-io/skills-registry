---
name: feature-planning
description: Focused task planning for ONE specific idea. Uses narrow→broad pattern - starts at user's focus point, expands context only as needed. Creates detailed task via task-board skill. Use when this capability is needed.
metadata:
  author: andersnygaard
---

# Feature Planning

Focused planning skill for ONE specific task the user has in mind. Starts narrow at user's focus point, expands outward only as needed.

**Pattern**: Narrow→Broad (start focused, expand as needed)

**🚨 PLANNING ONLY - NO IMPLEMENTATION**
This skill creates ONE task file. It does NOT write code or start implementation.

**Use `/discover-tasks` instead** for bulk backlog generation (broad→narrow pattern).

## When to Use This Skill

**Use this skill when**:
- User has a specific idea: "I want to fix X" or "Add feature Y"
- Working on ONE focused task
- Running `/new-task` slash command

**DO NOT use this skill for**:
- Bulk backlog generation → use `backlog-scan` skill
- Finding ALL gaps in codebase → use `backlog-scan` skill
- Implementing tasks → this skill only plans

## Context Loading (Area-Specific)

Load ONLY context relevant to user's focus area:

```
┌─────────────────────────────────┐
│        CONTEXT LOADING          │
│  • AREA rules only              │
│  • AREA done/ tasks only        │
│  • RELEVANT skill detection     │
└─────────────────────────────────┘
```

### Step 1: Identify Area

Based on user's input, determine area:
- **frontend** - UI, pages, components, CSS
- **backend** - API routes, services, database
- **components** - shared component library
- **e2e** - tests

### Step 2: Load Area Rules

```
.claude/rules/{area}/
```

Only load rules for identified area. Don't load everything.

### Step 3: Check Area Done Tasks

Scan `.task-board/done/` for tasks related to this area:
- Recent patterns
- How similar work was done
- Quality standards

### Step 4: Detect Implementation Skill

Based on task type, note which skill to use:

| Task Type | Skill |
|-----------|-------|
| UI/CSS work | `frontend-design` |
| API routes | `node-backend` |
| Components | `storybook-stories` |
| E2E tests | Standard Playwright |

## Research Pattern: Narrow→Broad

Start at user's specific point, expand ONLY when necessary:

```
User's focus (component, file, screenshot)
           ↓
Direct dependencies (imports, props, CSS)
           ↓
Related components (siblings, parent)
           ↓
Broader patterns (design system, architecture)
           ↓
STOP when you have enough context
```

### Example: "Column width in last column"

1. **Start**: SpreadsheetTable component
2. **Check**: Component CSS, props, column logic
3. **Maybe**: Related table components
4. **Only if needed**: Design system tokens
5. **STOP**: Don't scan entire frontend

### Key Principle

**Only expand when necessary.** Don't:
- Read CLAUDE.md entirely (only relevant sections)
- Scan all features
- Load all rules
- Check all done tasks

## Workflow

### Phase 1: Ask User

```
What do you want to work on?
```

**STOP and wait for response.**

### Phase 2: Context Loading

1. Identify area from user's response
2. Load area-specific rules
3. Check related done/ tasks
4. Detect implementation skill

### Phase 3: Focused Research

Research outward from user's starting point:

1. Read the specific file/component mentioned
2. Check direct dependencies
3. Expand only if needed for understanding
4. Stop when context is sufficient

### Phase 4: Propose Task

Present focused proposal:

```
**Proposed Task:** [Clear title]
**Type:** FEATURE / BUG / REFACTOR
**Why:** [1-2 sentences]
**Scope:**
- Includes: [what's in]
- Excludes: [what's out]
**Skill:** [frontend-design / node-backend / none]

Create this task?
```

**STOP and wait for approval.**

### Phase 5: Delegate to task-board

If approved, invoke `task-board` skill with:
- Task title and type
- Research findings (files, patterns)
- Scope boundaries
- Implementation skill to use

### Phase 6: End

After task-board creates the file, output exactly:

```
✓ Task created: [filename]

Ready in backlog. To implement, use `start-working` skill or ask directly.
```

Then **STOP**. Do not:
- Offer to implement
- Ask follow-up questions
- Suggest next steps

The user will initiate the next action.

## Rules

- **ONE task at a time**
- **Ask before researching**
- **Ask before creating**
- **Start narrow, expand only as needed**
- **Load area rules BEFORE researching**
- **Note implementation skill for task file**

## Delegates To

- `task-board` skill - for detailed plan file creation

## See Also

- `backlog-scan` skill - for bulk discovery (broad→narrow)
- `.claude/rules/` - domain rules by area
- `.task-board/done/` - completed task patterns

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/andersnygaard) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-12 -->
