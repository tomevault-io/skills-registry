---
name: review-standard
description: Three-phase code review covering documentation completeness, code quality and reuse, and advanced analysis like type safety and change scope. Use when this capability is needed.
metadata:
  author: ayazhankadessova
---

# Code Review

A structured, repeatable review process that checks documentation quality, code reuse, and deeper quality signals before changes land on the main branch.

## Philosophy

- **Consistent**: Same process every time, regardless of change size
- **Standards-driven**: Clear rules for what passes and what doesn't
- **Reuse-oriented**: Catch duplicated work and missed utilities
- **Actionable**: Every finding includes a concrete fix, not just a complaint
- **Context-aware**: Judge changes within the broader architecture

## What Gets Assessed

1. **Documentation completeness** — are docs present and accurate?
2. **Code quality and reuse** — are best practices followed? Are existing utilities leveraged?
3. **Advanced quality** — unnecessary indirection, type safety, module focus, change scope

Reviews produce recommendations. The final merge decision stays with maintainers.

## How to Run a Review

1. Collect the changed files and full diff
2. Run Phase 1 (documentation)
3. Run Phase 2 (code quality and reuse)
4. Run Phase 3 (advanced analysis)
5. Compile the report

---

## Phase 1: Documentation

### 1.1 Comment and Doc Content Quality

**Standard**: Documentation should explain current design rationale, not compare against previous versions.

**Violations** (avoid these):
- "Reduces 30% LOC compared to the old approach"
- "Simplified from X to Y lines"

**Good examples**:
- "Provides a unified interface across modules"
- "Caches results to avoid redundant API calls"

### 1.2 Folder READMEs

**Standard**: Every non-hidden directory should have a `README.md`.

**Check**: For each directory touched by the change, verify a `README.md` exists and reflects any new files.

### 1.3 Source Interface Documentation

**Standard**: Source files should have companion `.md` files describing their interfaces.

**Check**: Verify the `.md` file covers:
- **External interface** — public APIs, signatures, inputs/outputs, error conditions
- **Internal helpers** — private functions, non-obvious algorithms

### 1.4 Test Documentation

**Standard**: Test files should explain what they verify.

**Acceptable formats**: inline comments (for simple tests) or a companion `.md` file (for complex suites).

### 1.5 Design Documentation

**Standard**: Architectural changes should be backed by design docs in `docs/`.

**Expected for**: new subsystems, major features, significant restructuring.

### 1.6 Documentation Linting

If the project has a documentation linter, verify the changes would pass it.

---

## Phase 2: Code Quality and Reuse

### 2.1 Duplication Within the Change

**Goal**: Spot repeated logic, similar function bodies, or duplicated validation/error handling inside the new code.

### 2.2 Reuse of Project Utilities

**Goal**: Check whether existing helpers (in `src/utils/`, `scripts/`, `lib/`, etc.) already solve what the new code is reimplementing.

### 2.3 Reuse of External Libraries

**Goal**: Identify custom code that a well-known library already handles.

Common examples:
- Hand-rolled argument parsing → `argparse` / `yargs`
- Manual HTTP requests → `requests` / `axios`
- Custom date handling → `dateutil` / `dayjs`
- DIY config parsing → `configparser` / `yaml`

### 2.4 Dependency Hygiene

**Goal**: Flag redundant or conflicting dependencies.

### 2.5 Project Conventions

**Goal**: Verify the change follows existing patterns — error handling, naming, module layout, logging, configuration.

### 2.6 Commit Hygiene

**Goal**: Ensure no inappropriate files or leftover debug code are included.

**Inappropriate files**: `.tmp`, `*.swp`, `*.bak`, `*.pyc`, `__pycache__/`, `build/`, `.vscode/`, `.idea/`, `.env`, `*.local.*`

**Debug leftovers**: stray `print`/`console.log` statements, commented-out debug blocks, breakpoints (`pdb.set_trace()`, `debugger`), hardcoded test data in production files.

---

## Phase 3: Advanced Quality

### 3.1 Unnecessary Indirection

**Goal**: Flag wrappers and abstractions that don't earn their keep.

Look for:
- Classes that only delegate to another class
- Functions that wrap a single call without adding logic
- Abstractions introduced before there's a second use case

### 3.2 Repetition Patterns

**Goal**: Decide whether repeated code should be generalised.

**Rule of thumb**: Extract when the pattern appears 3+ times and the abstraction is clean. Flag premature abstraction that adds more complexity than it removes.

### 3.3 Module Focus

**Goal**: Each module should have a clear, single responsibility.

Watch for:
- Unrelated features piggybacking on an existing module
- Logic that belongs in a different part of the codebase
- Catch-all utility modules growing without bounds

### 3.4 Interface Boundaries

**Goal**: Public contracts should be explicit and well-separated from implementation.

Watch for:
- `getattr(obj, 'field')` where direct attribute access would work
- Null/None checks scattered across callers instead of handled at the boundary
- Mandatory and optional fields mixed without a clear data contract (prefer typed structures like dataclasses or interfaces)

### 3.5 Type Safety and Magic Numbers

**Goal**: Enforce type annotations and named constants.

- Functions should have parameter and return type annotations
- Literal numbers (86400, 3600, 1024) should be replaced with named constants or enums

### 3.6 Change Scope

**Goal**: Ensure the change is appropriately scoped.

| Change type | Expected scope |
|------------|---------------|
| Feature addition | 1–3 modules |
| Bug fix | 1–2 files |
| Refactor | Broader impact acceptable if explicitly stated |
| API change | Multiple files, should be documented |

---

## Report Format

Every finding **must** reference the phase and check it relates to.

```
Location: src/utils/validator.py:12
Standard: Phase 1, Check 3 — Source Interface Documentation
Recommendation: Create validator.md documenting the public validate() function
```

### Report Template

```markdown
# Code Review Report

**Branch**: feature-branch-name
**Changed files**: N files (+X, -Y lines)
**Review date**: YYYY-MM-DD

---

## Phase 1: Documentation

### Passed
- [Items that pass]

### Issues
- [Findings with location, standard, recommendation]

### Warnings
- [Minor concerns]

---

## Phase 2: Code Quality and Reuse

[Same structure]

---

## Phase 3: Advanced Quality

[Same structure]

---

## Overall Assessment

**Status**: APPROVED / NEEDS CHANGES / CRITICAL ISSUES

**Summary**: X critical issues, Y warnings

**Actions before merge**:
1. [Action]
2. [Action]

**Merge readiness**: Ready / Not ready
```

### Verdict Definitions

- **APPROVED** — Documentation complete, no quality concerns. Ready to merge.
- **NEEDS CHANGES** — Minor gaps or improvements needed. Can merge after addressing them.
- **CRITICAL ISSUES** — Missing required documentation or significant quality problems. Must be resolved first.

### What a Good Finding Looks Like

Every issue should include:
1. **Location** — file path and line number
2. **Standard** — which phase and check it falls under
3. **Problem** — what's wrong and why it matters
4. **Recommendation** — concrete steps to fix it
5. **Example** — a code snippet when it helps

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ayazhankadessova) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
