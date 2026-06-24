---
name: subagent-driven-development
description: Use when executing implementation plans with independent tasks. Features 3-stage review: spec compliance, code quality, then UX.
metadata:
  author: bgrober
---

# Subagent-Driven Development

Execute plan by dispatching fresh subagent per task, with **three-stage review** after each:
1. Spec compliance review
2. Code quality review (ios-reviewer or backend-reviewer)
3. UX review

**Core principle:** Fresh subagent per task + three-stage review = high quality with great UX

## When to Use

- Have an implementation plan with defined tasks
- Tasks are mostly independent (can be done in any order)
- Want to stay in the current session (no parallel sessions)

## The 3-Stage Review Process

```
┌─────────────────────────────────────────────────────────────┐
│                    PER-TASK WORKFLOW                        │
├─────────────────────────────────────────────────────────────┤
│  1. IMPLEMENT      Implementer subagent builds the feature  │
│         ↓                                                   │
│  2. SPEC REVIEW    Does code match requirements?            │
│         ↓          (./spec-reviewer-prompt.md)              │
│  3. CODE REVIEW    Is code quality good?                    │
│         ↓          (ios-reviewer or backend-reviewer)       │
│  4. UX REVIEW      Is user experience good?                 │
│         ↓          (ux-reviewer)                            │
│  5. COMPLETE       Task marked done                         │
└─────────────────────────────────────────────────────────────┘
```

### Stage Details

**Stage 1: Implementation**
- Dispatch implementer subagent with full task text
- Implementer asks questions if unclear
- Implementer implements, tests, self-reviews, commits

**Stage 2: Spec Compliance Review**
- Verify implementer built what was requested (nothing more, nothing less)
- If issues found → implementer fixes → re-review
- Only proceed when ✅ spec compliant

**Stage 3: Code Quality Review**
- Use `ios-reviewer` for Swift/SwiftUI code
- Use `backend-reviewer` for Supabase/TypeScript code
- Check patterns, performance, security
- If issues found → implementer fixes → re-review
- Only proceed when ✅ code approved

**Stage 4: UX Review**
- Use `ux-reviewer` for all user-facing code
- Check flow clarity, feedback states, accessibility
- If issues found → implementer fixes → re-review
- Only proceed when ✅ UX approved

## Prompt Templates

- `./implementer-prompt.md` - Dispatch implementer subagent
- `./spec-reviewer-prompt.md` - Dispatch spec compliance reviewer
- `./code-quality-reviewer-prompt.md` - Dispatch code quality reviewer (uses ios-reviewer or backend-reviewer agent)
- `./ux-reviewer-prompt.md` - Dispatch UX reviewer

## Choosing the Right Code Reviewer

| Code Type | Agent |
|-----------|-------|
| Swift, SwiftUI, SwiftData, iOS | `ios-reviewer` |
| TypeScript, Supabase, Edge Functions, RLS | `backend-reviewer` |
| Mixed (full-stack feature) | Both in sequence |

## Example Workflow

```
You: I'm using Subagent-Driven Development to execute this plan.

[Read plan file: docs/plans/feature-plan.md]
[Extract all tasks, create TodoWrite]

Task 1: Add pour deletion with confirmation

[Dispatch implementer with full task text]

Implementer:
  - Added delete button to PourDetailView
  - Confirmation dialog implemented
  - SwiftData deletion with sync
  - 3 tests passing
  - Committed

[Dispatch spec reviewer]
Spec reviewer: ✅ Spec compliant - all requirements met

[Dispatch ios-reviewer for code quality]
iOS reviewer:
  Strengths: Good SwiftData usage, proper @MainActor
  Issues (Important): Missing haptic feedback on delete

[Implementer fixes]
Implementer: Added haptic feedback

[Re-dispatch ios-reviewer]
iOS reviewer: ✅ Approved

[Dispatch ux-reviewer]
UX reviewer:
  Strengths: Clear confirmation dialog, good error message
  Issues (Important): No undo option after deletion
  Suggestions: Add "Deleted" toast with undo button

[Implementer fixes]
Implementer: Added undo toast with 5-second window

[Re-dispatch ux-reviewer]
UX reviewer: ✅ UX approved - excellent recovery flow

[Mark Task 1 complete]

Task 2: ...
```

## Advantages

**Quality gates:**
- Self-review catches obvious issues
- Spec review prevents over/under-building
- Code review ensures technical quality
- **UX review ensures user success** (often missed by technical reviews)

**Fresh context:**
- Each subagent starts clean (no accumulated confusion)
- Implementer can ask questions before starting
- Reviewers evaluate objectively

**Fast iteration:**
- No human-in-loop between tasks
- Issues caught early (cheaper than debugging later)
- Review loops ensure fixes actually work

## Red Flags

**Never:**
- Skip any of the three review stages
- Proceed with unfixed issues from any reviewer
- Start code quality review before spec compliance passes
- Start UX review before code quality passes
- Dispatch multiple implementation subagents in parallel
- Make subagent read plan file (provide full text instead)
- Accept "close enough" on any review stage

**If reviewer finds issues:**
- Same implementer subagent fixes them
- Same reviewer re-reviews
- Repeat until approved
- Don't skip re-reviews

## Integration

**Required skills:**
- `indie-stack:writing-plans` - Creates the plan this skill executes
- `indie-stack:requesting-code-review` - Code review guidance

**Reviewers use:**
- `indie-stack:ios-reviewer` - Swift/SwiftUI code quality
- `indie-stack:backend-reviewer` - Supabase/TypeScript quality
- `indie-stack:ux-reviewer` - User experience quality

**Implementers should use:**
- `indie-stack:testing-flexible` - Testing guidance (encouraged, not blocking)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/bgrober) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
