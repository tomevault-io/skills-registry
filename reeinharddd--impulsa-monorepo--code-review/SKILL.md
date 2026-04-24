---
name: code-review
description: Perform automated code review against project standards and patterns. Use when reviewing code, checking for issues, or when the user asks for a code review. Use when this capability is needed.
metadata:
  author: reeinharddd
---

# Code Review Skill

> **Purpose:** Perform automated code review against project standards.

## Trigger

**When:** PR review requested OR user asks for review
**Context Needed:** Changed files, diff content, PR description
**MCP Tools:** `get_errors`, `grep_search`, `read_file`

## Checks

### TypeScript

- [ ] No `any` types
- [ ] Explicit return types on public methods
- [ ] No unused imports
- [ ] Proper error handling

### Architecture

- [ ] Controllers are thin
- [ ] Business logic in services
- [ ] DTOs for all inputs

### Angular (if frontend)

- [ ] Standalone components
- [ ] OnPush change detection
- [ ] Signals for state
- [ ] @if/@for control flow

### Testing

- [ ] Tests for new code
- [ ] No skipped tests
- [ ] Coverage maintained

### Documentation

- [ ] Updated if needed
- [ ] Correct template used

## Severity

| Level      | Action     |
| :--------- | :--------- |
| 🔴 Error   | Must fix   |
| 🟡 Warning | Should fix |
| 🔵 Info    | Consider   |

## Reference

- [CONSTRUCTION-CHECKLIST.md](/docs/process/workflow/CONSTRUCTION-CHECKLIST.md)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/reeinharddd) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
