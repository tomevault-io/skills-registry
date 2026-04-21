---
name: writing-plans
description: Use when you have a spec or requirements for a multi-step task, before touching code
metadata:
  author: stodesirnit976-bit
---

# Writing Plans

## Language
- Default output language: **中文（简体）**.
- If the user explicitly requests English (or another language), follow the user’s request.
- For code/comments/identifiers: keep whatever language is conventional for the codebase; do not translate API names, file paths, or exact error messages.


## Overview

Write a practical implementation plan before touching code. Assume the engineer has limited context of the codebase. The plan should be actionable: what to change, where, and how to validate it.

Principles:
- **DRY / YAGNI:** do the simplest thing that works.
- **Tests are pragmatic:** add/adjust tests when they meaningfully reduce risk or lock in behavior—skip “test for every line.”
- **Validation is required:** every task includes a way to verify progress (build, lint, smoke test, minimal repro, etc.).
- **Commit in milestones:** commit after a coherent chunk of value (not after every micro-step).

**Announce at start:** "I'm using the writing-plans skill to create the implementation plan."

**Context:** Prefer a dedicated worktree (optional but recommended).

**Save plans to:** `docs/plans/YYYY-MM-DD-<feature-name>.md`

## Task Granularity

Aim for **tasks that take ~10–30 minutes** each. Each task should deliver a concrete artifact (code change, config update, doc, or validation proof).

If something would take longer:
- Split it by component (API → UI → integration), or
- Split it by risk (scaffold → happy path → edge cases).

## Plan Document Header

Every plan MUST start with this header:

```markdown
# [Feature Name] Implementation Plan

**Goal:** [One sentence describing what this builds]

**Architecture:** [2-3 sentences about approach]

**Tech Stack:** [Key technologies/libraries]

---
```

## Task Structure

Use this structure per task (keep it short, but specific):

```markdown
### Task N: [Component / Change]

**Files:**
- Create: `exact/path/to/file.ext`
- Modify: `exact/path/to/existing.ext:123-145`
- (Optional) Tests: `tests/...` or `__tests__/...`

**Implementation:**
- [Bulleted, concrete edits. Include key function names, flags, env vars, or config keys.]

**Validation (required):**
- Run: `command ...`
- Expect: [what “good” looks like / what output changes]

**(Optional) Tests (when worth it):**
- Add/Update: `...`
- Run: `command ...`
- Expect: PASS

**Commit (recommended):**
- `git add ...`
- `git commit -m "feat: ..."`
```

## When to Add Tests (Rule of Thumb)

Prefer tests when:
- You’re fixing a regression or bug that could reoccur.
- The change is a pure function / logic with clear inputs/outputs.
- You’re touching parsing, transforms, permissions/auth, money/time math, or safety-critical logic.
- You’re refactoring and want a guardrail.

Skip or defer tests when:
- The change is mostly wiring, UI layout, or a throwaway spike.
- There’s already strong coverage and you’re making a small, low-risk tweak.
- Writing a test would cost more than the feature itself—then rely on a solid smoke check.

## Remember
- Exact file paths whenever possible.
- Prefer “copy-paste runnable” commands.
- Be explicit about acceptance criteria for each task.
- Reference other skills with `@skill-name` only if genuinely useful.

## Execution Handoff

After saving the plan, offer execution choice:

**"Plan complete and saved to `docs/plans/<filename>.md`. Two execution options:**

**1. Implement now (this session)** - execute task-by-task with lightweight checkpoints

**2. Hand off** - someone else implements, using this plan as the checklist

**Which approach?"**

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/stodesirnit976-bit) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
