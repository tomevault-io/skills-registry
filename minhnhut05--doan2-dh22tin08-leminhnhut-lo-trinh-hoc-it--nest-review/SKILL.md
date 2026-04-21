---
name: nest-review
description: Review NestJS code for best practices, security, and maintainability. Use when reviewing backend code. Use when this capability is needed.
metadata:
  author: minhnhut05
---

# Review NestJS Code

Perform a comprehensive code review on NestJS code following best practices.

> **Important:** Follow the Learning Mode guidelines in `_templates/learning-mode.md`

## Arguments
- `$ARGUMENTS` - File path, folder path, or "staged" for git staged changes

## Instructions

When the user runs `/nest-review <target>`:

### Step 1: Identify what to review
- If file path: Review that specific file
- If folder path: Review all `.ts` files in that folder
- If "staged": Review `git diff --staged` changes
- If nothing: Ask user what to review

### Step 2: Perform review with checklist

#### Security Review
- [ ] No hardcoded secrets or credentials
- [ ] Input validation on all endpoints (DTOs with class-validator)
- [ ] Proper authentication guards applied
- [ ] No SQL injection risks (using Prisma parameterized queries)
- [ ] Sensitive data not logged

#### Architecture Review
- [ ] Single Responsibility Principle followed
- [ ] Proper separation: Controller → Service → Repository pattern
- [ ] Dependencies injected correctly
- [ ] Module properly exports what's needed

#### NestJS Best Practices
- [ ] Decorators used correctly (@Injectable, @Controller, etc.)
- [ ] Proper exception handling (NestJS built-in exceptions)
- [ ] DTOs used for request/response transformation
- [ ] Async/await pattern consistent

#### Code Quality
- [ ] TypeScript types properly defined (no `any`)
- [ ] Consistent naming conventions
- [ ] No dead code or unused imports
- [ ] Comments where logic is complex

#### Performance
- [ ] No N+1 query issues
- [ ] Proper use of Prisma includes/selects
- [ ] Large data sets paginated

### Step 3: Report findings

Format:
```
## 🔍 Code Review: [file/folder name]

### ✅ What's Good
- Point 1
- Point 2

### ⚠️ Suggestions (non-blocking)
1. **[Category]**: Description
   - Current: `code snippet`
   - Suggested: `improved code`
   - Why: explanation

### 🚨 Issues (should fix)
1. **[Category]**: Description
   - Problem: what's wrong
   - Fix: how to fix
   - Why: explanation

### 📚 Learning Points
- Concept 1: brief explanation
- Concept 2: brief explanation
```

### Step 4: Interactive discussion
After report, ask:
- "Bạn có muốn tôi giải thích kỹ hơn issue nào không?"
- "Bạn có muốn tôi fix issue nào không?"

## Review Levels

User can specify level:
- `/nest-review file.ts quick` - Quick review (security + critical only)
- `/nest-review file.ts full` - Full review (all categories)
- `/nest-review file.ts` - Default: full review

## After Completion

Remind user:
- "Nhớ update TRACKPAD.md với best practices mới học được!"
- Link to NestJS docs for patterns mentioned

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/minhnhut05) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
