---
name: subagent-driven-development
description: Use when executing implementation plans with independent tasks. Dispatches subagents per task with two-stage review (spec then quality).
metadata:
  author: zebbern
---

# Subagent-Driven Development

Execute plan by dispatching subagents per task, with two-stage review after each: spec compliance review first, then code quality review.

**Core principle:** Fresh subagent per task + two-stage review (spec then quality) = high quality, fast iteration

## When to Use

Use when:

- You have an implementation plan with independent tasks
- Tasks can be executed one at a time
- You want automated review between tasks

## The Process

### For Each Task:

1. **Dispatch Implementer Subagent**
   - Provide full task specification
   - Subagent implements, tests, commits

2. **Dispatch Spec Reviewer Subagent**
   - Reviews if code matches specification
   - If gaps found → Implementer fixes

3. **Dispatch Code Quality Reviewer Subagent**
   - Reviews code quality, patterns, best practices
   - If issues found → Implementer fixes

4. **Mark Task Complete**
   - Update todo list
   - Move to next task

## Example Workflow

```
You: I'm using Subagent-Driven Development to execute this plan.

[Read plan file once: docs/plans/feature-plan.md]
[Extract all 5 tasks with full text and context]
[Create todo list with all tasks]

Task 1: Hook installation script

[Get Task 1 text and context (already extracted)]
[Dispatch implementation subagent with full task text + context]

Implementer: "Before I begin - should the hook be installed at user or system level?"

You: "User level (~/.config/hooks/)"

Implementer: "Got it. Implementing now..."
[Later] Implementer:
- Implemented install-hook command
- Added tests, 5/5 passing
- Self-review: Found I missed --force flag, added it
- Committed

[Dispatch spec compliance reviewer]
Spec reviewer: ✅ Spec compliant - all requirements met, nothing extra

[Get git SHAs, dispatch code quality reviewer]
Code reviewer: Strengths: Good test coverage, clean. Issues: None. Approved.

[Mark Task 1 complete]

Task 2: Recovery modes

[Get Task 2 text and context (already extracted)]
[Dispatch implementation subagent with full task text + context]

Implementer: [No questions, proceeds]
Implementer:
- Added verify/repair modes
- 8/8 tests passing
- Self-review: All good
- Committed

[Dispatch spec compliance reviewer]
Spec reviewer: ❌ Issues:
- Missing: Progress reporting (spec says "report every 100 items")
- Extra: Added --json flag (not requested)

[Implementer fixes issues]
Implementer: Removed --json flag, added progress reporting

[Spec reviewer reviews again]
Spec reviewer: ✅ Spec compliant now

[Dispatch code quality reviewer]
Code reviewer: Strengths: Solid. Issues (Important): Magic number (100)

[Implementer fixes]
Implementer: Extracted PROGRESS_INTERVAL constant

[Code reviewer reviews again]
Code reviewer: ✅ Approved

[Mark Task 2 complete]

...

[After all tasks]
[Dispatch final code-reviewer]
Final reviewer: All requirements met, ready to merge

Done!
```

## Advantages

**vs. Manual execution:**

- Subagents follow TDD naturally
- Fresh context per task (no confusion)
- Parallel-safe (subagents don't interfere)
- Subagent can ask questions (before AND during work)

**vs. Executing Plans:**

- Same session (no handoff)
- Continuous progress (no waiting)
- Review checkpoints automatic

**Quality gates:**

- Self-review catches issues before handoff
- Two-stage review: spec compliance, then code quality
- Review loops ensure fixes actually work
- Spec compliance prevents over/under-building
- Code quality ensures implementation is well-built

## Red Flags

**Never:**

- Skip reviews (spec compliance OR code quality)
- Proceed with unfixed issues
- Dispatch multiple implementation subagents in parallel (conflicts)
- Make subagent read plan file (provide full text instead)
- Skip scene-setting context (subagent needs to understand where task fits)
- Ignore subagent questions (answer before letting them proceed)
- Accept "close enough" on spec compliance (spec reviewer found issues = not done)
- Skip review loops (reviewer found issues = implementer fixes = review again)
- Let implementer self-review replace actual review (both are needed)
- **Start code quality review before spec compliance is ✅** (wrong order)
- Move to next task while either review has open issues

**If subagent asks questions:**

- Answer clearly and completely
- Provide additional context if needed
- Don't rush them into implementation

**If reviewer finds issues:**

- Implementer (same subagent) fixes them
- Reviewer reviews again
- Repeat until approved
- Don't skip the re-review

**If subagent fails task:**

- Dispatch fix subagent with specific instructions
- Don't try to fix manually (context pollution)

## Integration

**Works with these skills:**

- **writing-plans** - Creates the plan this skill executes
- **test-driven-development** - Subagents follow TDD for each task
- **verification-before-completion** - Ensure all verification passes

**Alternative workflow:**

- **executing-plans** - Use for simpler execution without subagent dispatch

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/zebbern) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
