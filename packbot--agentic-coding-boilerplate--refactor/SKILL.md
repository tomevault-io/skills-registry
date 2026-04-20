---
name: refactor
description: Refactor code safely following the Analyze > Plan > Refactor > Verify workflow. The key rule is that behavior must not change. Use when improving code structure, readability, or modularity. Use when this capability is needed.
metadata:
  author: packbot
---

# Refactor: $ARGUMENTS

The key rule: **behavior must not change**.

## Phase 1: Analyze

- Read the code to be refactored: **$ARGUMENTS**
- Understand its current behavior, inputs, outputs, and side effects
- Identify all callers and dependents of the code
- Map out the test coverage — which behaviors are tested?
- If test coverage is insufficient, write tests FIRST before refactoring

## Phase 2: Plan

- Define what "better" means for this refactoring (readability? performance? modularity?)
- Outline the specific changes you'll make
- Identify risks — what could break?
- Plan to make changes in small, independently verifiable steps
- Present your plan and wait for approval

## Phase 3: Refactor

For each step:
1. Make one logical change at a time
2. Run tests after each change to verify behavior is preserved
3. If a test fails, revert the change and re-analyze
4. Keep the git history clean — each step should be a meaningful, working state

Guidelines:
- Extract, don't rewrite — prefer extracting functions/modules over rewriting from scratch
- Rename for clarity — if names don't communicate intent, rename them
- Reduce complexity — break large functions into smaller, focused ones
- Remove duplication — but only when the duplication is true (same concept, not just similar code)

## Phase 4: Verify

- [ ] All existing tests pass: `{{TEST_COMMAND}}`
- [ ] Behavior is identical — no functional changes
- [ ] Linting passes: `{{LINT_COMMAND}}`
- [ ] Build succeeds: `{{BUILD_COMMAND}}`
- [ ] Code is measurably better by the metric defined in Phase 2
- [ ] No new dependencies introduced
- [ ] All callers and dependents still work correctly

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/packbot) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
