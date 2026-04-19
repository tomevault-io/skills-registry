---
name: plan-create
description: Create modular implementation plans as chunked files. Use when you have requirements for a multi-step task and need to break it into context-friendly chunks. Requires superpowers plugin for design specs. Use when this capability is needed.
metadata:
  author: tzenderman
---

# Plan Create

## Prerequisites

This skill requires the `superpowers` plugin:
- Use `superpowers:brainstorming` to explore requirements first
- Use `superpowers:writing-plans` for high-level design specs
- Use THIS skill (`ddd-workflow:plan-create`) to break designs into implementation chunks

## Overview

Write implementation plans as **multiple small files** instead of one monolithic document. Each task gets its own file, making plans loadable by Claude Code without context overflow.

**Announce at start:** "Using ddd-workflow:plan-create to create a modular implementation plan."

**Save plans to:** `docs/plans/YYYY-MM-DD-<feature-name>/`

## Directory Structure

```
docs/plans/
  00-conventions.md                 # Shared patterns, naming, detail requirements
  YYYY-MM-DD-feature-name/
    00-overview.md                  # Goal, architecture, task index
    01-task-name.md                 # First task with TDD steps
    02-task-name.md                 # Second task
    ...
```

> **Note:** `00-conventions.md` lives at the `docs/plans/` root so it is shared across all plan folders.

## Workflow

```
┌─────────────────────────────────────────────────────────┐
│                   PLANNING WORKFLOW                      │
├─────────────────────────────────────────────────────────┤
│                                                         │
│   1. Create/update docs/plans/00-conventions.md          │
│   2. Create 00-overview.md (goal, architecture, index)  │
│   3. For each chunk:                                    │
│      a. Read docs/plans/00-conventions.md first         │
│      b. Write the chunk                                 │
│      c. New pattern? → ASK USER → maybe update conv.    │
│      d. Every 10 chunks → run extraction agent          │
│   4. After all chunks → invoke plan-lint skill          │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

---

## Step 1: Create or Update docs/plans/00-conventions.md

**Create this BEFORE writing any task chunks.** This is the single source of truth, shared across all plan folders.

If `docs/plans/00-conventions.md` already exists from a previous plan, read it first and extend it with any new conventions needed for this plan. If it doesn't exist, create it.

Use the template from `schemas/conventions-template.md`:

```markdown
# Plan Conventions

> **For Claude:** Read this BEFORE writing any chunk. ASK USER before updating.

## Naming Conventions
- Methods: `[verb]_[noun]` (e.g., `create_order`, `validate_input`)
- Guard clauses: `ensure_[condition]` (e.g., `ensure_authenticated`)
- Tests: `test_[method]_[scenario]` (e.g., `test_create_order_empty_cart`)
- Files: `[domain]_[type].py` (e.g., `order_service.py`)

## Design Patterns
- [List patterns as they're established]

## Detail Requirements
**Every task file MUST include:**
- [ ] Exact test code with assertions (not "write tests for X")
- [ ] Exact implementation code (not "implement the functionality")
- [ ] Expected test output
- [ ] Exact file paths
- [ ] Commit message

## Cross-Cutting Design Decisions
| Decision | Rationale | Applies To |
|----------|-----------|------------|
| [To be filled as decisions are made] | | |
```

---

## Step 2: Create 00-overview.md

```markdown
# [Feature Name] Implementation Plan

> **For Claude:** Load individual task files as needed. Use superpowers:executing-plans workflow.

**Goal:** [One sentence]

**Architecture:** [2-3 sentences about approach]

**Tech Stack:** [Key technologies]

---

## Tasks

| # | Task | File | Status |
|---|------|------|--------|
| 1 | [Component Name] | [01-component-name.md](01-component-name.md) | Pending |
| 2 | [Next Component] | [02-next-component.md](02-next-component.md) | Pending |
...

## Dependencies

- Task 2 depends on Task 1
- Tasks 3-4 can run in parallel
```

---

## Step 3: Write Task Chunks

### Before Writing Each Chunk

1. **Read `docs/plans/00-conventions.md`** - Ensure you follow established patterns
2. Write the chunk following the template below
3. **Check for new patterns** - If you made a decision that could apply to other chunks:
   - STOP
   - Ask user: "I used `[pattern]` for `[purpose]`. Should I add this to conventions?"
   - If approved, update `docs/plans/00-conventions.md`
   - Then continue

### Task File Template

Use the template from `schemas/chunk-template.md`:

```markdown
# Task N: [Component Name]

**Files:**
- Create: `exact/path/to/file.py`
- Modify: `exact/path/to/existing.py:123-145`
- Test: `tests/exact/path/to/test.py`

---

## Design Decisions

**Why [key decision]?**
- Rationale point 1
- Rationale point 2

---

## Step 1: Write the failing test

```python
def test_specific_behavior():
    result = function(input)
    assert result == expected
```

## Step 2: Run test to verify it fails

Run: `pytest tests/path/test.py::test_name -v`
Expected: FAIL with "function not defined"

## Step 3: Write minimal implementation

```python
def function(input):
    return expected
```

## Step 4: Run test to verify it passes

Run: `pytest tests/path/test.py::test_name -v`
Expected: PASS

## Step 5: Commit

```bash
git add tests/path/test.py src/path/file.py
git commit -m "feat: add specific feature"
```
```

### Design Decisions Section

**Required when the chunk involves:**
- Architectural choices between alternatives
- Pattern selection (State, Strategy, Factory, etc.)
- Convention decisions that affect other chunks
- Trade-offs future readers should understand

See `schemas/design-decisions.md` for format details.

### Task Granularity

**Each step is one action:**
- "Write the failing test" - step
- "Run it to make sure it fails" - step
- "Implement minimal code" - step
- "Run tests" - step
- "Commit" - step

**Each task file should be <200 lines** to stay within context limits.

---

## Step 4: Periodic Extraction (Every 10 Chunks)

After writing chunks 10, 20, 30, etc., run an extraction pass:

1. **Spawn a subagent** to read all chunks written so far
2. **Extract implicit conventions** - patterns used but not documented
3. **For each found pattern:** Ask user if it should be added to `docs/plans/00-conventions.md`
4. Update conventions with approved patterns
5. Continue writing chunks

This catches convention drift before it compounds.

---

## Step 5: Run Plan Lint

After ALL chunks are written, invoke the plan-lint skill:

**"All chunks written. Now running ddd-workflow:plan-lint to verify consistency."**

The linter will:
- Check naming pattern consistency
- Verify detail levels (no "implement X" shortcuts)
- Check structural consistency
- Validate Design Decisions sections
- Flag length outliers
- Iterate fixes until clean (with user approval for convention changes)

---

## Remember

- Exact file paths always
- Complete code in plan (not "add validation")
- Exact commands with expected output
- One task per file
- DRY, YAGNI, TDD, frequent commits
- **Always ask user before updating conventions**
- **Include Design Decisions for non-trivial chunks**

---

## Execution Handoff

After plan-lint passes, run plan-review:

**"Plan-lint passed ✓. Now running ddd-workflow:plan-review for architectural evaluation."**

After plan-review passes, offer execution choice:

**"Modular plan saved to `docs/plans/<dirname>/`. All validations passed ✓**

**Execution options:**

**1. Subagent-Driven (this session)** - Fresh subagent per task, review between tasks

**2. Parallel Session (separate)** - Open new session, batch execution with checkpoints

**Which approach?"**

**If Subagent-Driven:** Use superpowers:subagent-driven-development

**If Parallel Session:** New session uses superpowers:executing-plans, loading task files as needed

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tzenderman) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
