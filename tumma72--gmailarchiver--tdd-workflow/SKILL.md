---
name: tdd-workflow
description: >- Use when this capability is needed.
metadata:
  author: tumma72
---

# TDD Workflow for GMailArchiver

This skill provides guidance on the Test-Driven Development process.

## Source Documentation

**Always read the authoritative source:**

**`docs/PROCESS.md`** - Development workflow containing:
- Phase 3: Test (TDD Red) - Writing failing tests first
- Phase 4: Code (TDD Green) - Making tests pass with minimal code
- Red-green-refactor cycle explanation
- Exit criteria for each phase
- Definition of done

## Red-Green-Refactor Cycle

1. **Red**: Write a failing test
   - Test describes expected behavior
   - Verify test actually fails

2. **Green**: Write minimal code to pass
   - Simplest solution that works
   - Don't over-engineer

3. **Refactor**: Improve code quality
   - Remove duplication
   - Improve naming
   - Verify tests still pass

## Related Skills

- **testing-guidelines** - For test patterns and fixtures (docs/TESTING.md)
- **coding-standards** - For code style (docs/CODING.md)

## Usage

When following TDD:
1. Read `docs/PROCESS.md` Phase 3 and 4 for the workflow
2. Use `/test` command for Phase 3 guidance
3. Use `/code` command for Phase 4 guidance
4. If process changes, update `docs/PROCESS.md` (not this skill)

The source documentation is the **single source of truth** - this skill just points you there.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tumma72) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
