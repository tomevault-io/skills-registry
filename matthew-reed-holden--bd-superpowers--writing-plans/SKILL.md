---
name: writing-plans
description: Use when you have a spec or requirements for a multi-step task, before touching code
metadata:
  author: matthew-reed-holden
---

# Writing Plans

## Overview

Write comprehensive implementation plans assuming the engineer has zero context for our codebase and questionable taste. Document everything they need to know: which files to touch for each task, code, testing, docs they might need to check, how to test it. Give them the whole plan as bite-sized tasks. DRY. YAGNI. TDD. Frequent commits.

Assume they are a skilled developer, but know almost nothing about our toolset or problem domain. Assume they don't know good test design very well.

**Announce at start:** "I'm using the writing-plans skill to create the implementation plan."

**Context:** This should be run in a dedicated worktree (created by brainstorming skill).

**Storage:** Plans are stored in beads epics (`.beads/`) with markdown reference files (`docs/plans/YYYY-MM-DD-<feature-name>.md`)

## Task Granularity and Structure

**Each task should be one focused component/feature (15-30 minutes).**

**Each step within a task is one action (2-5 minutes):**
- "Write the failing test" - step
- "Run it to make sure it fails" - step
- "Implement the minimal code to make the test pass" - step
- "Run the tests and make sure they pass" - step
- "Commit" - step

## Creating the Plan in Beads

You have two options for creating plans in beads:

### Method 1: Interactive (Recommended for Standard Plans)

Create the plan epic and tasks using bash commands step-by-step:

```bash
# Source the helper functions
source skills/lib/beads-helper.sh

# 1. Create the implementation plan epic
epic_id=$(beads_create_plan_epic \
    "Feature Name" \
    "One sentence describing what this builds" \
    "2-3 sentences about approach" \
    "Key technologies/libraries" \
    1)  # priority: 1=high

echo "Created epic: $epic_id"

# 2. Create each task under the epic
task1_id=$(beads_create_task "$epic_id" \
    "Task 1: Component Name" \
    "$(cat <<'EOF'
**Files:**
- Create: `exact/path/to/file.py`
- Modify: `exact/path/to/existing.py:123-145`
- Test: `tests/exact/path/to/test.py`

**Step 1: Write the failing test**

```python
def test_specific_behavior():
    result = function(input)
    assert result == expected
```

**Step 2: Run test to verify it fails**

Run: `pytest tests/path/test.py::test_name -v`
Expected: FAIL with "function not defined"

[... continue with all steps ...]
EOF
)" \
    2)  # priority: 2=normal

task2_id=$(beads_create_task "$epic_id" \
    "Task 2: Another Component" \
    "[Task description with files and steps]" \
    2)

# 3. Add dependencies between tasks
beads_add_dependency "$task2_id" "$task1_id"  # task2 depends on task1

# 4. View the plan
bd show "$epic_id"
bd list --parent "$epic_id"
```

### Method 2: Scripted (For Complex Plans with Many Tasks)

Use the helper script for plans with many tasks:

```bash
# 1. Create a tasks.json file describing all tasks
cat > tasks.json <<'EOF'
{
  "tasks": [
    {
      "title": "Task 1: Component Name",
      "description": "Full task description with files and steps...",
      "priority": 1,
      "dependencies": []
    },
    {
      "title": "Task 2: Another Component",
      "description": "Full task description...",
      "priority": 2,
      "dependencies": ["Task 1: Component Name"]
    }
  ]
}
EOF

# 2. Run the script
skills/writing-plans/scripts/create-plan-in-beads.sh \
    "Feature Name" \
    "One sentence goal" \
    "2-3 sentences architecture" \
    "Tech stack" \
    tasks.json

# Output will show epic ID and all task IDs
```

## Markdown Reference Document

After creating the plan in beads, create a markdown reference file for human readability:

**Location:** `docs/plans/YYYY-MM-DD-<feature-name>.md`

**Header format:**

```markdown
# [Feature Name] Implementation Plan

**Beads Epic:** bd-abc123  
**View:** `bd show bd-abc123`  
**List Tasks:** `bd list --parent bd-abc123`

> **For Claude:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task.

**Goal:** [One sentence describing what this builds]

**Architecture:** [2-3 sentences about approach]

**Tech Stack:** [Key technologies/libraries]

---
```

**Then include all tasks for reference:**

```markdown
### Task 1: Component Name

**Beads ID:** bd-xyz456

**Files:**
- Create: `exact/path/to/file.py`
- Modify: `exact/path/to/existing.py:123-145`

[... full task details ...]
```

## Task Structure

Tasks in beads contain the full implementation details. The markdown reference mirrors this structure:

```markdown
### Task N: [Component Name]

**Beads ID:** bd-xyz123

**Files:**
- Create: `exact/path/to/file.py`
- Modify: `exact/path/to/existing.py:123-145`
- Test: `tests/exact/path/to/test.py`

**Step 1: Write the failing test**

```python
def test_specific_behavior():
    result = function(input)
    assert result == expected
```

**Step 2: Run test to verify it fails**

Run: `pytest tests/path/test.py::test_name -v`
Expected: FAIL with "function not defined"

**Step 3: Write minimal implementation**

```python
def function(input):
    return expected
```

**Step 4: Run test to verify it passes**

Run: `pytest tests/path/test.py::test_name -v`
Expected: PASS

**Step 5: Commit**

```bash
git add tests/path/test.py src/path/file.py
git commit -m "feat: add specific feature"
```
```

## Remember
- Exact file paths always
- Complete code in plan (not "add validation")
- Exact commands with expected output
- Reference relevant skills with @ syntax
- DRY, YAGNI, TDD, frequent commits
- Each task has a beads ID for tracking
- Markdown is for documentation, beads is for execution

## Beads Integration

This skill creates implementation plan epics in beads with child tasks, plus markdown reference files.

**Workflow:**
1. Plan the task breakdown and dependencies
2. Create beads epic with tasks (interactive or scripted method)
3. Create markdown reference file linking to epic and tasks
4. Commit both to git (beads auto-saves in `.beads/`, markdown in `docs/plans/`)

**Beads Commands:**
- View plan: `bd show <epic-id>`
- List tasks: `bd list --parent <epic-id>`
- List ready tasks: `bd ready --parent <epic-id>` (no dependencies blocking)
- Update task: `bd update <task-id> --status in_progress`
- Show task: `bd show <task-id>`

**Markdown Reference:**
- Stored in `docs/plans/YYYY-MM-DD-<topic>.md`
- Contains epic and task links at top
- Includes full task descriptions for easy reading
- Human-readable, searchable, diff-friendly
- Auto-updated by executing-plans skill as work progresses

## Execution Handoff

After creating the plan in beads and saving the markdown reference, offer execution choice:

**"Plan complete! Created epic `<epic-id>` with N tasks. Markdown saved to `docs/plans/<filename>.md`. Two execution options:**

**1. Subagent-Driven (this session)** - I dispatch fresh subagent per task, review between tasks, fast iteration

**2. Parallel Session (separate)** - Open new session with executing-plans, batch execution with checkpoints

**Which approach?"**

**If Subagent-Driven chosen:**
- **REQUIRED SUB-SKILL:** Use superpowers:subagent-driven-development
- Stay in this session
- Fresh subagent per task + code review
- Pass epic ID to subagent system

**If Parallel Session chosen:**
- Guide them to open new session in worktree
- **REQUIRED SUB-SKILL:** New session uses superpowers:executing-plans
- Pass epic ID for task loading

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/matthew-reed-holden) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
