---
name: planning
description: Generates comprehensive implementation plans with bite-sized tasks. Use after brainstorming or when requirements are clear, before writing code.
metadata:
  author: theecoderahmed
---

# Planning Implementation

## When to use this skill
- When you have a clear design or set of requirements.
- Before starting any coding task that involves multiple steps or files.
- To break down complex features into manageable chunks.

## Workflow
1.  **Context**: Ensure you understand the goal and have a design/spec.
2.  **Header**: Start the plan with the standard header.
3.  **Task Breakdown**: Create bite-sized tasks (TVDD - Test, Verify, Implementation, Verify, Commit).
4.  **Review**: Present the plan for user approval.

## Instructions

### 1. Plan Document Structure
Save plans to: `docs/plans/YYYY-MM-DD-<feature-name>.md`

**Header Template:**
```markdown
# [Feature Name] Implementation Plan

**Goal:** [One sentence describing what this builds]
**Architecture:** [2-3 sentences about approach]
**Tech Stack:** [Key technologies/libraries]

---
```

### 2. Task Granularity
Each task should be tiny (2-5 minutes execution time) and follow this pattern:

```markdown
### Task N: [Component/Step Name]

**Files:**
- Create: `exact/path/to/file.ext`
- Modify: `exact/path/to/existing.ext`

**Step 1: Write the failing test**
[Code snippet]

**Step 2: Run test to verify failure**
Command: `npm test ...`
Expected: [Failure message]

**Step 3: Implementation**
[Code snippet]

**Step 4: Verify Pass**
Command: `npm test ...`

**Step 5: Commit**
Command: `git commit -m "..."`
```

### 3. Principles
*   **Atomic Steps**: "Write test", "Run test", "Implement", "Verify" are separate steps.
*   **Exact Paths**: Never be vague. Use full file paths.
*   **Code in Plan**: Include the exact code to be written whenever possible.
*   **YAGNI**: Do not verify or implement things not asked for.

## Resources
*   [Brainstorming Skill](../brainstorming/SKILL.md) - If requirements are unclear, go back to brainstorming.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/theecoderahmed) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
