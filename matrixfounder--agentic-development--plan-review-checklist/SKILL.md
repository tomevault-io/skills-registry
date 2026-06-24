---
name: plan-review-checklist
description: Detailed checklist for verifying Development Plans. Use when this capability is needed.
metadata:
  author: matrixfounder
---
# Plan Review Checklist

## 1. Use Case Coverage
- [ ] **Total Coverage:** Every Use Case mapped to >= 1 Task?
- [ ] **Traceability:** Coverage table exists?

## 2. Structure & Formalism
- [ ] **Stub-First:** Every component has specific "Stub" and "Impl" phases/tasks?
- [ ] **Dependencies:** Task order respects dependencies?
- [ ] **Phasing:** Clear stages (Structure -> Logic -> Test)?

## 3. Task Descriptions
- [ ] **Existence:** File exists for every task in `plan.md`?
- [ ] **Naming:** Matches `task-{ID}-{SubID}-{slug}.md`?
- [ ] **Sections:** Contains Goal, Changes, Test Cases, Acceptance Criteria?
- [ ] **Depth:** Specific file paths and method signatures? (Without coding).

- [ ] **Strict Mode:** Usage of `skill-tdd-strict` specified for critical components/bugs?

## Criticality Protocol
- 🔴 **BLOCKING:** Missing Use Case, Missing Task File, No "Stub-First" approach.
- 🟡 **MAJOR:** Missing coverage table, Vague dependencies.
- 🟢 **MINOR:** Formatting, missing "Notes".

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/matrixfounder) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
