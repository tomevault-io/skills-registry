---
name: plan-guideline
description: Build detailed implementation plans with file-level specificity, complexity estimates, and a docs-then-tests-then-code workflow. Use when this capability is needed.
metadata:
  author: ayazhankadessova
---

# Implementation Planning

Guides AI agents through creating thorough, actionable implementation plans for features, refactors, or bug fixes. A finished plan should read like a blueprint — specific enough that someone can start coding from it without guesswork.

## What Makes a Good Plan

- **Specific**: Names exact files and line ranges, not "audit the codebase"
- **Quantified**: Estimates size in lines of code, not hours or days
- **Ordered**: Documentation first, then tests, then implementation
- **Interface-aware**: Describes API and contract changes before diving into internals
- **Actionable**: Can be turned directly into a GitHub issue

## Planning Process

### 1. Understand the Goal

Read the user's requirements and pin down:
- A clear one- or two-sentence problem statement
- What "done" looks like (success criteria)
- What is explicitly out of scope

**For bug fixes**: attempt to reproduce the issue before designing a solution (skip if the fix is obvious, a failing test already exists, or reproduction would be unsafe).

### 2. Explore the Codebase

**This happens during planning, not as a step in the plan.** The plan must present findings, not "TODO: investigate" placeholders.

- Search for relevant files by name and content
- Read existing implementations of similar features
- Identify architectural patterns the project already follows
- Map module dependencies

**Good**: `Modify src/auth/handler.py:15-45 to add token validation`
**Bad**: `Step 1: Audit the codebase to find relevant files`

### 3. Design Interfaces

- Sketch new function/class signatures
- Note changes to existing interfaces and whether they break callers
- Identify which documentation files need updates
- Consider backward compatibility

### 4. Design the Test Strategy

- List existing test files that need changes
- Describe new tests and what each one validates
- For bug fixes: map reproduction steps to test cases where possible

### 5. Lay Out Implementation Steps

Use lines of code (LOC) to gauge complexity:

| Size | LOC |
|------|-----|
| Trivial | 1–20 |
| Small | 21–50 |
| Medium | 51–150 |
| Large | 151–400 |
| Very large | 401+ |

**Ordering is mandatory**:

1. **Documentation** — create or update docs describing the intended behaviour
2. **Tests** — write tests that verify the documented behaviour
3. **Code** — implement the feature or fix so the tests pass

Never put implementation before documentation or tests. Never combine multiple steps into one. Break any step larger than 400 LOC into smaller pieces.

## Plan Template

```markdown
# Implementation Plan: [Name]

## Goal
[Problem statement in 1–2 sentences]

**Success criteria:**
- [Criterion]

**Out of scope:**
- [Exclusion]

## Bug Reproduction
*(Only for bug fixes where reproduction was attempted)*

**Steps tried:**
- [Action]

**Observed symptoms:**
- [Error or behaviour]

**Root cause hypothesis:**
- [Diagnosis]

## Codebase Analysis

**Files to modify:**
- `path/to/file:lines` — Purpose

**Files to create:**
- `path/to/new_file` — Purpose (Estimated: X LOC)

**Files to delete:**
- `path/to/old_file` — Reason

**Architecture notes:**
[Key observations]

## Interface Design

**New interfaces:**
- [Signatures and descriptions]

**Modified interfaces:**
- [Before/after]

**Documentation changes:**
- [Doc files and sections]

## Test Strategy

**Existing tests to update:**
- `test/file:lines` — What changes
  - Test case: description

**New test files:**
- `test/new_file` — Purpose (Estimated: X LOC)
  - Test case: description

## Implementation Steps

**Step 1: [Description]** (Estimated: X LOC)
- File changes
- Dependencies: None

**Step 2: [Description]** (Estimated: X LOC)
- File changes
- Dependencies: Step 1

**Total estimated size:** X LOC ([complexity level])
```

## After the Plan Is Ready

1. Present it to the user for approval
2. Optionally create a GitHub issue from the approved plan
3. Begin implementation — create a branch, follow the steps in order, and open a PR when done

## Reminders

1. **Docs → Tests → Code** — always in that order
2. **No vague steps** — every step names files and line ranges
3. **LOC, not time** — never estimate in hours or days
4. **Tests before code** — design test cases before writing the implementation
5. **Break up large steps** — anything over 400 LOC should be split
6. **Follow existing patterns** — match the project's conventions
7. **Be precise** — `Modify parser.py:45-67 to handle nested objects` beats `Update the parser`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ayazhankadessova) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
