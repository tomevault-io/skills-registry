---
name: code-architecture-reviewer
description: Review code architecture for quality. Keywords: architecture, review, code. Use when this capability is needed.
metadata:
  author: willyu1007
---
# Code Architecture Reviewer

This workflow performs a thorough code review focused on architectural fit, correctness, and maintainability.

---

## Purpose & Scope

Use this workflow when:
- New code was added and needs review
- A refactor was completed and requires validation
- You want to proactively catch integration/performance/security risks

Out of scope:
- Implementing fixes without approval (unless the task explicitly requests fixes)

---

## Inputs & Preconditions

Inputs:
- Target files/dirs and their intended change
- Relevant SSOT docs and strategies (`AGENTS.md` + relevant skills under `/.system/skills/ssot/**`)

Preconditions:
- Identify the local scope and rules (closest applicable `AGENTS.md`).

---

## Steps

1. **Understand intent**
   - What problem is being solved and what should remain unchanged?
2. **Check boundaries**
   - Is the code in the correct module/scope?
   - Are responsibilities separated (UI vs domain vs data access, etc.)?
3. **Correctness**
   - Error handling, nullability, edge cases, async flows
4. **Consistency**
   - Naming, file organization, patterns, conventions
5. **Safety**
   - Security-sensitive behavior, permission checks, data handling
6. **Performance**
   - Obvious inefficiencies, hot paths, unnecessary re-renders/queries
7. **Write a review report**
   - Prioritize issues by severity and provide actionable suggestions.

---

## Outputs

- A review report (executive summary + prioritized findings + recommendations)
- Optional: a list of follow-up tasks or a revised plan

---

## Safety Notes

- If changes affect security/production behavior, require explicit human review before proceeding.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/willyu1007) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
