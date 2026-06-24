---
name: two-stage-review
description: This skill should be used after completing development tasks. Performs two-stage code review: Stage 1 verifies specification compliance (prevents scope creep), Stage 2 reviews code quality (ensures maintainability). Use when this capability is needed.
metadata:
  author: pyinx
---

# Two-Stage Code Review

## Purpose

Ensure code quality through a rigorous two-stage review process that catches both specification violations and maintainability issues.

## Process

### Stage 1: Specification Compliance Review

**Goal**: Prevent over-building and under-building

**Checklist**:
- ✅ All required features implemented (no missing functionality)
- ❌ No unauthorized features added (no scope creep)
- 📋 API design matches architecture document
- 📋 Data model matches specifications
- 📋 User stories fully satisfied

**Outcome**: ✅ Compliant or ❌ Non-compliant with specific issues

### Stage 2: Code Quality Review

**Goal**: Ensure maintainability and clarity

**Checklist**:
- 📐 Clear variable naming
- 📐 Single responsibility functions
- 🔧 No code duplication
- 🔧 Modular design
- 🧪 Adequate test coverage
- ⚡ No obvious performance issues
- 🛡️ Input validation
- 🛡️ Error handling

**Outcome**: ✅ Approved or ❌ Needs improvement with specific issues

## Review Loop

```
Implementation → Stage 1 Review → ❌ Fix spec issues → Repeat Stage 1
                                → ✅ Pass → Stage 2 Review → ❌ Fix quality issues → Repeat Stage 2
                                                              → ✅ Pass → Complete
```

## Usage

This skill is automatically invoked by the CEO workflow during Phase 4 (Development) after each subtask completion.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/pyinx) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
