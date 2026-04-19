---
name: task-implementation
description: Execute next task from Implementation Plan. Use when user asks to implement next task, start next feature, or continue implementation. Use when this capability is needed.
metadata:
  author: doraemonkeys
---

# Task Implementation

## Workflow

1. Review `docs/Implementation Plan.md` to identify next pending task by priority order
2. Review `docs/PROJECT_OVERVIEW.md` to understand project context
3. Assess task size and choose approach:
   - **Small**: Implement directly
   - **Medium**: Design complete solution before coding
   - **Large**: Review `.cursor\skills\task-splitting\SKILL.md` to break into 2-3 subtasks
4. Implement with edge case tests to maintain coverage (Design for Testability)
5. Run `make ci` to verify
6. Review `.cursor\skills\docs-maintenance\SKILL.md` to update documentation

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/doraemonkeys) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
