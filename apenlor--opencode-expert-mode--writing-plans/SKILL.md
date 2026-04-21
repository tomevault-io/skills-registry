---
name: writing-plans
description: Use when you have a spec or requirements for a multi-step task, before touching code
metadata:
  author: apenlor
---

# Writing Plans

## Overview
Write comprehensive implementation plans that give a skilled developer everything they need: which files to touch, what code to write, how to verify each step. Bite-sized tasks. DRY. YAGNI. Suggest frequent commits.

## When to Use
Use before starting any multi-step implementation. Essential for ensuring a structured and verifiable development process.

**Announce at start:** "I'm using the writing-plans skill to create the implementation plan."

**Output:** Present the full plan in the chat using a Markdown code block.

## Bite-Sized Task Granularity

Each step is one action (2–5 minutes):
- "Write the failing test" — step
- "Run it to confirm it fails" — step
- "Implement the minimal code to pass" — step
- "Run tests to confirm they pass" — step
- "Suggest commit" — step

Include test steps when the project has a test suite.

## Plan Document Header

Every plan MUST start with this header:

```markdown
# [Feature Name] Implementation Plan

> **For OpenCode:** Use the `executing-plans` skill to implement this plan task-by-task.

**Goal:** [One sentence describing what this builds]

**Architecture:** [2–3 sentences about approach]

**Tech Stack:** [Key technologies/libraries]

---
```

## Task Structure

```markdown
### Task N: [Component Name]

**Files:**
- Create: `exact/path/to/file.ts`
- Modify: `exact/path/to/existing.ts:123-145`
- Test: `tests/exact/path/to/test.ts`

**Step 1: [Action]**

[Code or command if needed]

**Step 2: Verify**

Run: `<test command>`
Expected: [what success looks like]

**Step 3: Suggest Commit**

Suggest: `git add <files> && git commit -m "feat: <description>"`
```

## Remember
- Exact file paths always
- Complete code in the plan (not "add validation")
- Exact commands with expected output
- DRY, YAGNI, suggest frequent commits

## Execution Handoff

After presenting the plan:

**"Plan complete. You can execute it with `/execute-plan`, or ask me to implement it directly."**

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/apenlor) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
