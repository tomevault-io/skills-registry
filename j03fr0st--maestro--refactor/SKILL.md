---
name: refactor
description: > Use when this capability is needed.
metadata:
  author: j03fr0st
---

# Refactor

Improve code structure and readability without changing external behavior. Refactoring is gradual evolution, not revolution -- use this for improving existing code, not rewriting from scratch.

## Principles

1. **Preserve behavior** -- refactoring changes structure, not what the code does
2. **Small steps** -- make one change, verify, repeat. Large jumps introduce bugs that are hard to trace back
3. **Commit at safe states** -- version control lets you roll back if a step goes wrong
4. **Tests first** -- without tests, you cannot verify behavior is preserved. If tests are missing, write them before refactoring
5. **Separate concerns** -- do not mix refactoring commits with feature changes, because it makes code review and rollback harder

## When to Refactor

Good reasons:
- Code is hard to understand or modify
- Duplicated logic across multiple locations
- Functions/classes doing too many things
- Adding a new feature requires touching unrelated code
- Test failures are hard to diagnose due to tangled logic

Skip refactoring when:
- Code works, is tested, and will not change again
- No tests exist and you cannot add them first (add tests first instead)
- You are under a tight deadline (note it as tech debt instead)
- There is no clear benefit -- "just because" is not a reason

## Safe Refactoring Process

### Step 1: Prepare

- Verify tests exist and pass. If tests are missing, write characterization tests that capture current behavior before making structural changes
- Commit current state so you have a clean rollback point
- Create a feature branch for larger refactors

### Step 2: Identify

- Find the specific smell or structural issue (see [patterns catalog](./references/patterns.md))
- Understand what the code does -- read callers, tests, and related modules
- Draft a refactoring plan before making changes

### Step 3: Execute (small steps)

- Make one focused change at a time
- Run tests after each change
- Commit when tests pass
- Repeat until the target structure is reached

### Step 4: Verify

- All tests pass (unit, integration, e2e)
- Manual testing for areas without automated coverage
- Performance is unchanged or improved (measure if relevant)

### Step 5: Clean up

- Update comments and documentation to match new structure
- Remove dead code and unused imports
- Final commit with clear description of what changed and why

## Output Format

Present a refactoring plan before making changes:

```
## Refactoring Plan

**Target:** [file/function/class being refactored]
**Smell:** [what structural issue was identified]
**Approach:** [which refactoring technique(s) to apply]
**Risk:** [what could break, and how tests cover it]

### Steps
1. [First change]
2. [Second change]
3. ...

### Expected Result
[Brief description of the improved structure]
```

Then execute the plan step by step, running tests between each change.

## Edge Cases

### Refactoring across multiple files

When a change touches many files (e.g., renaming a widely-used interface, extracting a shared utility):

- Map all call sites before starting -- use grep/find-references to build a complete list
- Change the definition and all usages in a single atomic step to avoid broken intermediate states
- If the change is too large for one step, use the "expand and contract" pattern: introduce the new API alongside the old one, migrate callers incrementally, then remove the old API

### Handling breaking changes

When refactoring public APIs or shared libraries:

- Deprecate the old API first (add deprecation warnings/annotations)
- Provide the new API alongside the old one during a migration period
- Update documentation and changelog
- Coordinate with downstream consumers before removing the old API

### Code without tests

When you encounter untested code that needs refactoring:

1. Write characterization tests first -- tests that capture current behavior, including edge cases and error paths
2. Only then begin structural changes
3. If writing full tests is impractical, at minimum add smoke tests for the critical path
4. Document any untested paths as known risk in the refactoring plan

## Refactoring Checklist

### Code Quality

- [ ] Functions are small (< 50 lines) and do one thing
- [ ] No duplicated code
- [ ] Names are descriptive (variables, functions, classes)
- [ ] No magic numbers or strings -- use named constants
- [ ] Dead code removed

### Structure

- [ ] Related code is grouped together
- [ ] Clear module boundaries
- [ ] Dependencies flow in one direction (no circular deps)

### Type Safety

- [ ] Types defined for all public APIs
- [ ] No untyped `any` without justification
- [ ] Nullable types explicitly marked

### Testing

- [ ] Refactored code is tested
- [ ] Tests cover edge cases
- [ ] All tests pass

## Patterns Catalog

For before/after examples of common code smells and design patterns, see **[references/patterns.md](./references/patterns.md)**. The catalog covers:

- Long Method/Function
- Duplicated Code
- Large Class/Module (God Object)
- Long Parameter List
- Feature Envy
- Primitive Obsession
- Magic Numbers/Strings
- Nested Conditionals (Arrow Code)
- Dead Code
- Inappropriate Intimacy
- Extract Method (detailed walkthrough)
- Introducing Type Safety
- Strategy Pattern
- Chain of Responsibility
- Full operations reference table

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/j03fr0st) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
