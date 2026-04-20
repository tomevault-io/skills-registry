---
name: code-review-assistant
description: Perform systematic code review following {{sharedLib}}/{{projectName}} HMI standards and best practices. Use when reviewing pull requests, checking code quality, or validating implementations before commit. Checks MobX patterns, UI components, architecture, testing, and common pitfalls. Use when this capability is needed.
metadata:
  author: biggs3d
---

# Code Review Assistant

This skill performs comprehensive code review following {{sharedLib}}/{{projectName}} HMI standards.

## When to Use

- Reviewing pull requests or merge requests
- Before creating a commit or PR
- Validating new implementations
- Checking code against standards
- Finding common mistakes before CI/CD

## Review Process

### Step 1: Read Review Standards (CRITICAL - Do This First!)

**ALWAYS start by reading the complete review checklist:**

```
Read ai-guide/_REFERENCE/REVIEW.md
```

This is the **Single Source of Truth** for all code review standards, patterns, and checks. It contains:
- MobX patterns and critical checks
- UI component requirements
- Property ViewModel patterns
- Entity ViewModel standards
- Architecture guidelines
- Testing requirements
- Common pitfalls to avoid
- Code quality standards

**Do not proceed without reading REVIEW.md** - it contains the most up-to-date guidance.

### Step 2: Identify Files to Review

#### Ask User First

- Are you reviewing an MR? (If yes, get MR number)
- Or reviewing local changes before creating MR?
- Specific files/directories to focus on?

#### For Merge Request Reviews

**Use `glab` CLI** to get the complete picture:
- MR description and author's intent
- Existing discussions and reviewer feedback
- CI/CD status
- Full diff with context

**Why this matters**: MR description tells you the "why" - without it, you might flag intentional decisions as mistakes.

#### For Local Reviews

Use `git diff` against the base branch (usually `origin/development`).

#### Files to Look For

Once you have the file list, identify:
- ViewModels (`*.ts` extending BaseViewModel)
- Views (`*.tsx` with React components)
- Entity ViewModels (`*EntityViewModel.ts`)
- Model files (`/model/*/src/`)
- Test files (`*.test.ts`, `*.test.tsx`)

### Step 3: Apply REVIEW.md Checklist

Follow **all categories** from REVIEW.md systematically:

1. **MobX Patterns** - Constructors, observables, actions, async operations
2. **UI Components** - {{sharedLib}} components, observer wrapper, SelectPortal
3. **Property ViewModels** - Correct VM types, commanded pattern, label converters
4. **Entity ViewModels** - Base classes, required methods, adapters
5. **Architecture** - MVVM separation, proper layering, services
6. **Testing** - Coverage, mocking, edge cases
7. **Common Pitfalls** - Known issues documented in COMMON_PITFALLS.md
8. **Code Quality** - Naming, file size, self-documenting code

**Important**: For each category in REVIEW.md, also read the referenced deep-dive documentation:
- `MOBX_ESSENTIALS.md` for MobX details
- `UI_COMPONENT_GUIDELINES.md` for UI standards
- `PROPERTY_VIEWMODEL_GUIDE.md` for property VM patterns
- `ENTITY_ARCHITECTURE.md` for entity VM standards
- `COMMON_PITFALLS.md` for known issues

### Step 4: Check Supporting Documentation

If reviewing specific areas, read the relevant guides:
- Model changes → Read `MODEL_GENERATION_GUIDE.md`
- Server code → Read `_SERVER/SERVER.md`
- Cross-stack changes → Read `CLIENT_SERVER_BRIDGE.md`

### Step 5: Provide Structured Feedback

**If reviewing an MR**: Include context from `glab mr view` at the top:
- MR title and description
- Stated goals/intent
- Any existing reviewer feedback to address

Structure your review output as:

```markdown
## Code Review Summary

**MR Context** (if applicable):
- MR #123: "Add tactical ballistic missile counting"
- Author's intent: Replace icon string parsing with enum-based filtering
- Existing feedback: None / [summarize existing comments]

### Critical Issues (Must Fix)
1. [File:Line] Issue description with reference to REVIEW.md section
   - Why: Explanation of impact
   - Fix: Suggested solution

### High Priority
1. [File:Line] Issue description
   - Why: Impact explanation
   - Fix: Suggested solution

### Medium Priority
1. [File:Line] Issue description
   - Why: Impact explanation
   - Fix: Suggested solution

### Low Priority / Suggestions
1. [File:Line] Suggestion
   - Why: Improvement reasoning
   - Consider: Alternative approach

### Positive Observations
- ✅ What was done well (be specific)
- ✅ Good patterns to highlight
- ✅ Commendable practices
```

## Severity Levels

Use these consistently based on impact:

- **CRITICAL**: Breaks build, violates core requirements, data loss risk, changes will be overwritten
- **HIGH**: Runtime errors likely, major functionality broken, security issues
- **MEDIUM**: Bugs possible, inconsistent with patterns, poor UX, maintainability concerns
- **LOW**: Code quality, minor inconsistencies, suggestions for improvement

## Questions to Ask User

Before starting the review:

- Which files/directories should I review?
- Full review or specific categories (MobX, UI, Architecture, etc.)?
- Should I suggest fixes or just identify issues?
- Are there specific concerns to focus on?
- Is this for a merge request review?

## Key References

When the skill is invoked, always read:

1. **[REVIEW.md](../../ai-guide/_REFERENCE/REVIEW.md)** - Complete review checklist (SSoT)
2. Supporting docs as needed based on what's being reviewed:
   - [MOBX_ESSENTIALS.md](../../ai-guide/_DAILY/MOBX_ESSENTIALS.md) - MobX patterns
   - [UI_COMPONENT_GUIDELINES.md](../../ai-guide/_DAILY/UI_COMPONENT_GUIDELINES.md) - UI standards
   - [PROPERTY_VIEWMODEL_GUIDE.md](../../ai-guide/_DAILY/PROPERTY_VIEWMODEL_GUIDE.md) - Property VMs
   - [ENTITY_ARCHITECTURE.md](../../ai-guide/_ARCHITECTURE/ENTITY_ARCHITECTURE.md) - Entity VMs
   - [COMMON_PITFALLS.md](../../ai-guide/_DAILY/COMMON_PITFALLS.md) - Known issues
   - [TESTING_GUIDE.md](../../ai-guide/_DAILY/TESTING_GUIDE.md) - Testing patterns

## Notes

- Always be constructive and specific in feedback
- Reference exact file locations with line numbers
- Cite which standard/pattern from documentation is violated
- Provide actionable fixes, not just criticism
- Acknowledge what's done well to encourage good patterns

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/biggs3d) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
