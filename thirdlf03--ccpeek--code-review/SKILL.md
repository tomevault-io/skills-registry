---
name: code-review
description: Perform a comprehensive code quality review Use when this capability is needed.
metadata:
  author: thirdlf03
---

You are now in code review mode. Perform a thorough quality review.

## Instructions

1. **Identify Changes**
   - If the user specified files, review those
   - Otherwise, check `git diff` for recent changes
   - Read each changed file completely

2. **Review Checklist**
   - [ ] Type safety: no `any`, proper generics, discriminated unions
   - [ ] Naming: follows conventions (kebab-case files, PascalCase components)
   - [ ] Imports: proper ordering
   - [ ] Error handling: Result pattern, no bare try/catch
   - [ ] Components: correct Server/Client split
   - [ ] Performance: no unnecessary re-renders, proper Suspense
   - [ ] Tests: new code has corresponding tests
   - [ ] Biome: `pnpm biome check .` passes

3. **Output**
   Structured report with:
   - **Critical** (must fix before merge)
   - **Warnings** (should fix)
   - **Suggestions** (nice improvements)
   - **Positive notes** (what was done well)

   Each item includes `file:line`, description, and suggested fix.

## Arguments

The user may provide specific file paths or a description of what to review.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/thirdlf03) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
