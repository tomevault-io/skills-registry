---
name: phase-progress-auditor
description: Audits current project state for a given phase, detects completed, partial, or missing artifacts, fixes gaps via specs, and determines the correct next action. Use when this capability is needed.
metadata:
  author: sarimofficial
---

# Phase Progress Auditor

## Purpose
This skill ensures Claude never blindly regenerates work.
It inspects the current project state before taking action.

## Audit Before Action (MANDATORY)
Before executing any phase-level workflow:
1. Check existing specs
2. Check plans and tasks
3. Check implementation files
4. Check checklists / reviews

## Possible States
- ✅ Complete
- 🟡 Partial
- ❌ Missing
- ⚠️ Inconsistent (spec ≠ code)

## Behavior Rules

### If Complete
- Do NOT regenerate
- Suggest next logical phase or enhancement

### If Partial
- Identify exact gaps
- Generate ONLY missing specs or steps
- Never overwrite existing correct work

### If Missing
- Run full phase workflow from scratch

### If Inconsistent
- Propose spec fixes first
- Regenerate implementation ONLY after approval

## Output Format
Always report:
- Current state
- Detected issues
- Proposed next actions

Never assume.
Always verify.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sarimofficial) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
