---
name: clavix-review
description: Review code changes with criteria-driven analysis (Security, Architecture, Standards, Performance). Use when reviewing PRs or code changes. Use when this capability is needed.
metadata:
  author: alysonhower
---
# Clavix Review Skill

Review code changes (PRs, branches) with criteria-driven analysis and structured feedback.

## What This Skill Does

1. **Ask for PR context** - Which branch or PR to review
2. **Gather review criteria** - What aspects to focus on (security, architecture, standards, etc.)
3. **Collect additional context** - Team conventions, specific concerns, or focus areas
4. **Analyze the diff** - Read changed files and surrounding context
5. **Generate review report** - Structured findings with severity levels
6. **Save the report** - To `.clavix/outputs/reviews/` for reference

**This is about analysis, not fixing. I report issues for you to address.**

---

## State Assertion (REQUIRED)

**Before starting review, output:**

```
**CLAVIX MODE: PR Review**
Mode: analysis
Purpose: Criteria-driven code review generating actionable feedback
Implementation: BLOCKED - I will analyze and report, not modify code
```

---

## Self-Correction Protocol

**DETECT**: If you find yourself doing any of these 6 mistake types:

| Type | What It Looks Like |
|------|--------------------|
| 1. Skipping Diff Analysis | Reviewing without actually reading the changed code |
| 2. Ignoring User Criteria | Checking all dimensions when user specified focus areas |
| 3. Vague Feedback | "Code could be better" instead of specific file:line issues |
| 4. False Positives | Flagging issues that follow existing project patterns |
| 5. Missing Context | Not considering existing conventions before flagging |
| 6. Implementation Mode | Starting to fix issues instead of just reporting them |

**STOP**: Immediately halt the incorrect action

**CORRECT**: Output:
"I apologize - I was [describe mistake]. Let me return to the review workflow."

**RESUME**: Return to the review workflow with correct approach.

---

## Phase 1: Context Gathering

### Step 1: Ask for PR Identification

```
What PR would you like me to review?

Options:
- Provide a branch name (I'll diff against main/master)
- Describe the feature/change and I'll help locate it

Example: "feature/user-authentication" or "the payment integration branch"
```

**If branch provided**: Confirm target branch for diff (default: main or master)

**If unclear**: Ask clarifying questions to identify the correct branch

### Step 2: Ask for Review Criteria

```
What aspects should I focus on?

Presets:
🔒 Security     - Auth, validation, secrets, XSS/CSRF, injection
🏗️ Architecture - Design patterns, SOLID, separation of concerns
📏 Standards    - Code style, naming, documentation, testing
⚡ Performance  - Efficiency, caching, query optimization
🔄 All-Around   - Balanced review across all dimensions

Or describe specific concerns (e.g., "error handling and input validation")
```

**CHECKPOINT:** Criteria selected: [list criteria]

### Step 3: Ask for Additional Context (Optional)

```
Any team conventions or specific concerns I should know about?

Examples:
- "We use Repository pattern for data access"
- "All endpoints must have input validation"
- "Check for proper error handling"
- "We require 80% test coverage"

(Press Enter to skip)
```

**CHECKPOINT:** Context gathered, ready to analyze

---

## Phase 2: Diff Retrieval

### Get the Diff

```bash
git diff <target-branch>...<source-branch>
```

Or if on the feature branch:
```bash
git diff <target-branch>
```

### If Diff Retrieval Fails

- Check if branch exists: `git branch -a | grep <branch-name>`
- Suggest alternatives: "I couldn't find that branch. Did you mean [similar-name]?"
- Offer manual input: "You can paste the diff or list of changed files"

### Categorize Changed Files

- **Source code**: `.ts`, `.js`, `.py`, etc.
- **Tests**: `*.test.*`, `*.spec.*`
- **Config**: `.json`, `.yaml`, `.env.*`
- **Documentation**: `.md`, `.txt`

Prioritize based on selected criteria (e.g., security → auth files first)

**CHECKPOINT:** Retrieved diff with [N] changed files

---

## Phase 3: Criteria-Based Analysis

For each selected criterion, systematically check the diff:

### 🔒 Security Analysis

- Authentication checks on protected routes
- Authorization/permission verification
- Input validation and sanitization
- No hardcoded secrets, keys, or tokens
- XSS prevention (output encoding)
- CSRF protection on state changes
- SQL injection prevention (parameterized queries)
- Safe dependency usage

### 🏗️ Architecture Analysis

- Separation of concerns maintained
- Coupling between components
- Cohesion within modules
- SOLID principles adherence
- Consistent design patterns
- No layer violations (e.g., UI calling DB directly)
- Dependency direction toward abstractions

### 📏 Standards Analysis

- Descriptive, consistent naming
- Meaningful comments where needed
- Reasonable function/method length
- DRY principle (no duplication)
- Consistent code style
- Clear error messages
- Appropriate logging

### ⚡ Performance Analysis

- N+1 query detection
- Appropriate caching
- Lazy loading where applicable
- Proper resource cleanup
- Efficient algorithms/data structures

### 🧪 Testing Analysis

- New code has tests
- Edge cases covered
- Error scenarios tested
- Test quality and readability
- Integration tests for critical paths

**IMPORTANT:**
- Only analyze criteria the user selected
- Consider existing project patterns before flagging
- Read surrounding code for context
- Be specific: include file name and line number

**CHECKPOINT:** Analysis complete for [criteria list]

---

## Phase 4: Report Generation

Generate the review report following this exact structure:

```markdown
# PR Review Report

**Branch:** `{source-branch}` → `{target-branch}`
**Files Changed:** {count}
**Review Criteria:** {selected criteria}
**Date:** {YYYY-MM-DD}

---

## 📊 Executive Summary

| Dimension | Rating | Key Finding |
|-----------|--------|-------------|
| {Criterion 1} | 🟢 GOOD | {one-line summary} |
| {Criterion 2} | 🟡 FAIR | {one-line summary} |
| {Criterion 3} | 🔴 NEEDS WORK | {one-line summary} |

**Overall Assessment:** {Approve / Approve with Minor Changes / Request Changes}

---

## 🔍 Detailed Findings

### 🔴 Critical (Must Fix)

| ID | File | Line | Issue |
|:--:|:-----|:----:|:------|
| C1 | `{file}` | {line} | {specific issue description} |

{If none: "No critical issues found."}

### 🟠 Major (Should Fix)

| ID | File | Line | Issue |
|:--:|:-----|:----:|:------|
| M1 | `{file}` | {line} | {specific issue description} |

{If none: "No major issues found."}

### 🟡 Minor (Nice to Fix)

| ID | File | Line | Issue |
|:--:|:-----|:----:|:------|
| N1 | `{file}` | {line} | {specific issue description} |

{If none: "No minor issues found."}

### ✅ Positive Notes

- {Good practice observed in file:line}
- {Pattern worth highlighting}

---

## 💡 Recommendations

1. **Immediate:** {Critical fixes needed before merge}
2. **Before merge:** {Major improvements to address}
3. **Consider:** {Minor enhancements for code quality}
```

---

## Severity Levels

| Level | Symbol | Meaning |
|-------|--------|---------|
| Critical | 🔴 | Must fix before merge, security/data risk |
| Major | 🟠 | Should fix, significant issue |
| Minor | 🟡 | Nice to fix, quality improvement |
| Positive | ✅ | Good practices worth noting |

---

## Save Location

Review reports: `.clavix/outputs/reviews/{branch}-{YYYY-MM-DD}.md`

---

## Difference from /clavix-verify

| /clavix-verify | /clavix-review |
|----------------|----------------|
| Checks YOUR code against YOUR PRD | Reviews code changes against criteria |
| Requirement-focused | Code quality-focused |
| Pass/fail on features | Severity-based findings |
| Self-verification | Peer review |

---

## Mode Boundaries

**What I'll do:**
- ✓ Ask clarifying questions about the PR and review focus
- ✓ Retrieve and analyze the git diff
- ✓ Check code against selected criteria
- ✓ Generate structured review report with severity levels
- ✓ Save the report for reference
- ✓ Highlight both issues and good practices

**What I won't do:**
- ✗ Fix issues in the code
- ✗ Approve or merge the PR
- ✗ Skip the criteria gathering phase
- ✗ Make changes to the codebase

**We're reviewing code, not modifying it.**

---

## Troubleshooting

### Issue: Branch not found
**Solution**: Run `git fetch origin`, check spelling with `git branch -a | grep <name>`

### Issue: Diff is empty
**Solution**: Confirm correct branches, check if already merged

### Issue: Too many false positives
**Solution**: Read more context, check project patterns, ask about team conventions

### Issue: User wants me to fix issues
**Solution**: Remind this is review mode; suggest separate session to implement fixes

---

## Next Steps

After review: Share report with team, track critical/major fixes, re-review after changes

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/alysonhower) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
