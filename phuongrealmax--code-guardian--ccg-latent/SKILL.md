---
name: ccg-latent
description: Use this skill for multi-step development tasks. Latent Chain Mode reduces token usage by 70-80% through hidden-state reasoning with 4-phase workflow: analysis → plan → impl → review.
metadata:
  author: phuongrealmax
---

# CCG Latent Chain Mode

Token-efficient hidden-state reasoning for complex development tasks.

## When to Use

**MANDATORY** for tasks with 2+ steps:
- Bug fixes requiring code analysis
- Feature implementations
- Refactoring across files
- Code reviews and audits

## 3 Quick Flows

| Flow | Command | Use Case |
|------|---------|----------|
| **A** | `/latent-fix` | Quick fix 1-2 patches |
| **B** | `/latent-feature` | Multi-file feature/refactor |
| **C** | `/latent-review` | Code review without edits |

## 4-Phase Workflow

```
ANALYSIS ──► PLAN ──► IMPL ──► REVIEW
   🔍          📋        🔧        ✅
```

### Phase 1: Analysis 🔍
- Understand requirements
- Identify hot spots (file:line)
- Document constraints and risks

### Phase 2: Plan 📋
- Break into subtasks/patches
- Order by dependencies
- Estimate complexity

### Phase 3: Implementation 🔧
- Apply patches with `latent_apply_patch`
- Run `guard_validate` after each patch
- Run `testing_run_affected` for verification

### Phase 4: Review ✅
- Verify all changes
- Check against constraints
- Complete task

## Core Tools

```
latent_context_create    - Start new task context
latent_context_get       - Retrieve current context
latent_context_update    - Merge delta (changes only!)
latent_phase_transition  - Move to next phase
latent_apply_patch       - Apply code changes (unified diff)
latent_complete_task     - Mark task complete
latent_status            - Get module status
```

## Output Format

**Human-readable format in editor:**

```
🔍 [analysis] <title>
<1-2 sentence description>

[Hot Spots] file:line, file:line
[Decisions] D001: ..., D002: ...
[Risks] if any

---

📋 [plan] <N patches/tasks>

[Patches]
1. file:line - description
2. file:line - description

---

🔧 [impl] Patch N/M: <name>
Applied: <count> | Tests: <status>

---

✅ [review] Complete
Files: N | Patches: M | Tests: passed
```

## Context Delta Format

**CRITICAL:** Only send changes, never full context!

```json
{
  "summary": "Brief description (max 200 chars)",
  "contextDelta": {
    "codeMap": { "hotSpots": ["src/auth.ts:45"] },
    "decisions": [{ "id": "D001", "summary": "Use JWT", "rationale": "Industry standard" }],
    "risks": ["Token expiry handling"]
  },
  "actions": [
    { "type": "edit_file", "target": "src/auth.ts", "description": "Fix token validation" }
  ]
}
```

## Strict Rules

1. **Summary max 200 chars** - No essays
2. **Delta only** - Never repeat full context
3. **Decision IDs** - D001, D002 for tracking
4. **Always complete** - Don't leave contexts hanging
5. **Guard + Test** - After every patch
6. **Phase icons** - 🔍 📋 🔧 ✅

## Example: Bug Fix Flow

```
User: "Fix the login timeout bug"

Claude:
1. latent_context_create({ taskId: "fix-login-timeout" })
2. 🔍 [analysis] Investigate login timeout
   [Hot Spots] src/auth/login.ts:145
   [Decisions] D001: Root cause is missing token refresh
3. latent_phase_transition({ toPhase: "plan" })
4. 📋 [plan] 2 patches
   1. src/auth/login.ts:145 - Add token refresh logic
   2. src/auth/login.ts:160 - Handle refresh errors
5. latent_phase_transition({ toPhase: "impl" })
6. latent_apply_patch({ target: "src/auth/login.ts", patch: "..." })
7. guard_validate({ code: "...", filename: "login.ts" })
8. latent_phase_transition({ toPhase: "review" })
9. ✅ [review] Complete - Files: 1 | Patches: 2 | Tests: passed
10. latent_complete_task({ summary: "Fixed login timeout with token refresh" })
```

## Token Savings

| Traditional | Latent Mode | Savings |
|-------------|-------------|---------|
| Full context each turn | Delta only | 70-80% |
| Verbose explanations | Structured output | 50-60% |
| Repeated code | References | 40-50% |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/phuongrealmax) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
