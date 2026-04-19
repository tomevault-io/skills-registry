---
name: code-review
description: Use when completing tasks, implementing major features, or before merging. Covers both performing self-review and responding to external review feedback.
metadata:
  author: jevon
---

# Code Review

## When to Review

**Mandatory:**
- After each task in a plan
- After completing a major feature
- Before merge to main

**Optional but valuable:**
- When stuck (fresh perspective)
- Before refactoring
- After fixing complex bug

## Self-Review Checklist

Before marking any task complete, review your own work:

### Spec Compliance
- [ ] Implemented everything requested — nothing missing
- [ ] Built nothing extra — no unrequested features (YAGNI)
- [ ] Interpreted requirements correctly
- [ ] Didn't solve the wrong problem

### Code Quality
- [ ] Names clear and accurate (match what things do)
- [ ] Code clean and maintainable
- [ ] Following existing codebase patterns
- [ ] No over-engineering
- [ ] No duplication

### Testing
- [ ] Tests verify behavior, not mock behavior
- [ ] TDD followed (test-first, watched fail)
- [ ] Edge cases and errors covered
- [ ] All tests pass (verified fresh, not assumed)

### Git Hygiene
- [ ] Changes committed with clear messages
- [ ] No unrelated changes bundled
- [ ] No debug code or temporary files

## Requesting Human Review

When presenting work for review:

```
## What was implemented
[Brief description]

## Changes
[Files changed, key decisions]

## Verification
[Test output, verification evidence]

## Concerns
[Anything uncertain or worth discussing]
```

Use `todo summary` to show overall progress.

## Receiving Review Feedback

### The Response Pattern

1. **READ** — Complete feedback without reacting
2. **UNDERSTAND** — Restate the requirement in own words (or ask)
3. **VERIFY** — Check against codebase reality
4. **EVALUATE** — Technically sound for THIS codebase?
5. **RESPOND** — Technical acknowledgment or reasoned pushback
6. **IMPLEMENT** — One item at a time, test each

### Forbidden Responses

- "You're absolutely right!" (performative)
- "Great point!" (performative)
- "Let me implement that now" (before verification)

**Instead:** Restate the technical requirement. Ask clarifying questions. Just fix it.

### When to Push Back

Push back when:
- Suggestion breaks existing functionality
- Reviewer lacks full context
- Violates YAGNI (unused feature)
- Technically incorrect for this stack
- Conflicts with prior architectural decisions

**How:** Use technical reasoning. Reference working tests/code. Ask specific questions.

### Implementation Order

For multi-item feedback:
1. Clarify anything unclear FIRST
2. Then implement:
   - Blocking issues (breaks, security)
   - Simple fixes (typos, imports)
   - Complex fixes (refactoring, logic)
3. Test each fix individually
4. Verify no regressions

### Acknowledging Correct Feedback

```
✅ "Fixed. [Brief description]"
✅ "Good catch — [issue]. Fixed in [location]."
✅ [Just fix it and show the code]

❌ Long apologies
❌ Defending why you were wrong
❌ Over-explaining
```

State the correction factually and move on.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jevon) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
