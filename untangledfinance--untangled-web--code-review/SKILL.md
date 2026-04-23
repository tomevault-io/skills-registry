---
name: code-review
description: Use when receiving code review feedback (especially if unclear or technically questionable), when completing tasks or major features requiring review before proceeding, or before making any completion/success claims. Covers three practices - receiving feedback with technical rigor over performative agreement, requesting reviews via code-reviewer subagent, and verification gates requiring evidence before any status claims. Essential for subagent-driven development, pull requests, and preventing false completion claims.
metadata:
  author: untangledfinance
---

# Code Review

Guide proper code review practices emphasizing technical rigor, evidence-based claims, and verification over performative responses.

## Overview

Code review requires three distinct practices:

1. **Receiving feedback** - Technical evaluation over performative agreement
2. **Requesting reviews** - Systematic review via code-reviewer subagent
3. **Verification gates** - Evidence before any completion claims

Each practice has specific triggers and protocols detailed in reference files.

## Core Principle

Always honoring **YAGNI**, **KISS**, and **DRY** principles.
**Be honest, be brutal, straight to the point, and be concise.**

**Technical correctness over social comfort.** Verify before implementing. Ask before assuming. Evidence before claims.

## When to Use This Skill

### Receiving Feedback

Trigger when:

- Receiving code review comments from any source
- Feedback seems unclear or technically questionable
- Multiple review items need prioritization
- External reviewer lacks full context
- Suggestion conflicts with existing decisions

**Reference:** `references/code-review-reception.md`

### Requesting Review

Trigger when:

- Completing tasks in subagent-driven development (after EACH task)
- Finishing major features or refactors
- Before merging to main branch
- Stuck and need fresh perspective
- After fixing complex bugs

**Reference:** `references/requesting-code-review.md`

### Verification Gates

Trigger when:

- About to claim tests pass, build succeeds, or work is complete
- Before committing, pushing, or creating PRs
- Moving to next task
- Any statement suggesting success/completion
- Expressing satisfaction with work

**Reference:** `references/verification-before-completion.md`

## Quick Decision Tree

```
SITUATION?
â
ââ Received feedback
â  ââ Unclear items? â STOP, ask for clarification first
â  ââ From human partner? â Understand, then implement
â  ââ From external reviewer? â Verify technically before implementing
â
ââ Completed work
â  ââ Major feature/task? â Request code-reviewer subagent review
â  ââ Before merge? â Request code-reviewer subagent review
â
ââ About to claim status
   ââ Have fresh verification? â State claim WITH evidence
   ââ No fresh verification? â RUN verification command first
```

## Receiving Feedback Protocol

### Response Pattern

READ â UNDERSTAND â VERIFY â EVALUATE â RESPOND â IMPLEMENT

### Key Rules

- â No performative agreement: "You're absolutely right!", "Great point!", "Thanks for [anything]"
- â No implementation before verification
- â Restate requirement, ask questions, push back with technical reasoning, or just start working
- â If unclear: STOP and ask for clarification on ALL unclear items first
- â YAGNI check: grep for usage before implementing suggested "proper" features

### Source Handling

- **Human partner:** Trusted - implement after understanding, no performative agreement
- **External reviewers:** Verify technically correct, check for breakage, push back if wrong

**Full protocol:** `references/code-review-reception.md`

## Requesting Review Protocol

### When to Request

- After each task in subagent-driven development
- After major feature completion
- Before merge to main

### Process

1. Get git SHAs: `BASE_SHA=$(git rev-parse HEAD~1)` and `HEAD_SHA=$(git rev-parse HEAD)`
2. Dispatch code-reviewer subagent via Task tool with: WHAT_WAS_IMPLEMENTED, PLAN_OR_REQUIREMENTS, BASE_SHA, HEAD_SHA, DESCRIPTION
3. Act on feedback: Fix Critical immediately, Important before proceeding, note Minor for later

**Full protocol:** `references/requesting-code-review.md`

## Verification Gates Protocol

### The Iron Law

**NO COMPLETION CLAIMS WITHOUT FRESH VERIFICATION EVIDENCE**

### Gate Function

IDENTIFY command â RUN full command â READ output â VERIFY confirms claim â THEN claim

Skip any step = lying, not verifying

### Requirements

- Tests pass: Test output shows 0 failures
- Build succeeds: Build command exit 0
- Bug fixed: Test original symptom passes
- Requirements met: Line-by-line checklist verified

### Red Flags - STOP

Using "should"/"probably"/"seems to", expressing satisfaction before verification, committing without verification, trusting agent reports, ANY wording implying success without running verification

**Full protocol:** `references/verification-before-completion.md`

## Integration with Workflows

- **Subagent-Driven:** Review after EACH task, verify before moving to next
- **Pull Requests:** Verify tests pass, request code-reviewer review before merge
- **General:** Apply verification gates before any status claims, push back on invalid feedback

## Bottom Line

1. Technical rigor over social performance - No performative agreement
2. Systematic review processes - Use code-reviewer subagent
3. Evidence before claims - Verification gates always

Verify. Question. Then implement. Evidence. Then claim.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/untangledfinance) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
