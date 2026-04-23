---
name: ai-code-quality
description: > Use when this capability is needed.
metadata:
  author: adiomas
---

# AI Code Quality Score

Semantic code quality analysis using LLM capabilities. Goes beyond syntax checking
to understand if code is actually **good**.

## Why This Matters

Traditional tools check:
- Syntax errors (compiler)
- Style violations (linter)
- Type errors (type checker)

But they **cannot** check:
- Is this code maintainable?
- Does it follow project patterns?
- Is it unnecessarily complex?
- Will future developers understand it?

This skill fills that gap with AI-powered semantic analysis.

## Quality Dimensions

### 1. Complexity Score (1-10)

Measures cognitive load required to understand the code.

**Factors:**
- Cyclomatic complexity (branching)
- Nesting depth
- Function length
- Number of parameters
- Callback chains / promise nesting

**Scoring:**
| Score | Level | Action |
|-------|-------|--------|
| 1-3 | Simple | ✅ Good |
| 4-6 | Moderate | ⚠️ Consider simplifying |
| 7-8 | Complex | ⛔ Refactor recommended |
| 9-10 | Very Complex | 🚫 Must refactor |

### 2. Maintainability Index (1-10)

How easy will it be to modify this code in 6 months?

**Factors:**
- Clear naming conventions
- Single responsibility
- Proper abstraction level
- Documentation quality
- Test coverage

**Scoring:**
| Score | Level | Meaning |
|-------|-------|---------|
| 1-3 | Poor | Technical debt accumulating |
| 4-6 | Acceptable | Some improvements needed |
| 7-8 | Good | Easy to maintain |
| 9-10 | Excellent | Self-documenting, well-tested |

### 3. Architecture Compliance (1-10)

Does the code follow project patterns?

**Analysis:**
1. Read existing codebase patterns
2. Compare new code to patterns
3. Flag deviations

**Examples:**
```
✅ "Uses the same error handling pattern as other API routes"
⚠️ "Direct database calls - project uses repository pattern"
❌ "Mixing business logic in UI component - violates separation"
```

### 4. Duplication Score (1-10)

DRY (Don't Repeat Yourself) compliance.

**Detection:**
- Exact code clones
- Structural similarity
- Logic duplication with different names

**Scoring:**
| Score | Duplication | Action |
|-------|-------------|--------|
| 9-10 | None | ✅ Perfect |
| 7-8 | Minor | ⚠️ Consider extracting |
| 4-6 | Moderate | ⛔ Extract to shared utility |
| 1-3 | Severe | 🚫 Major refactor needed |

### 5. Overall Health Score (1-10)

Weighted average of all dimensions.

**Formula:**
```
health = (complexity * 0.25) +
         (maintainability * 0.30) +
         (architecture * 0.25) +
         (duplication * 0.20)
```

## Analysis Protocol

### Step 1: Identify Changed Files

```bash
# Get files changed in current work
git diff --name-only HEAD~1 | grep -E '\.(ts|tsx|js|jsx|py|go|rs)$'
```

### Step 2: Read Project Patterns

Before analyzing, understand project conventions:

```bash
# Read existing similar files for pattern comparison
# Example: If analyzing a new API route, read existing routes
ls src/api/**/*.ts | head -3 | xargs cat
```

### Step 3: Analyze Each File

For each changed file, perform semantic analysis:

```markdown
## Analysis: src/components/UserForm.tsx

### Complexity Score: 6/10 (Moderate)

**Findings:**
- Function `validateForm` has 8 conditional branches
- Nested ternary on line 45 reduces readability
- 3 levels of callback nesting in `handleSubmit`

**Recommendation:**
- Extract validation logic to separate function
- Replace nested ternary with early returns
- Use async/await instead of nested callbacks

### Maintainability Score: 7/10 (Good)

**Findings:**
- Clear component naming
- Props are typed
- Missing JSDoc for complex validation rules

**Recommendation:**
- Add JSDoc comments explaining validation rules
- Consider extracting form state to custom hook

### Architecture Compliance: 8/10 (Good)

**Findings:**
- Follows project's component structure
- Uses project's form library correctly
- ✅ Validation matches other forms

**Deviation:**
- Direct API call in component (other components use hooks)

**Recommendation:**
- Move API call to `useUserMutation` hook

### Duplication Score: 9/10 (Excellent)

**Findings:**
- No significant code duplication detected
- Reuses existing validation utilities

### Overall Health: 7.5/10 (Good)

This code is **production-ready** with minor improvements recommended.
```

### Step 4: Generate Quality Report

```markdown
# AI Code Quality Report

## Summary

| File | Complexity | Maintainability | Architecture | Duplication | Health |
|------|------------|-----------------|--------------|-------------|--------|
| UserForm.tsx | 6 | 7 | 8 | 9 | **7.5** |
| api/users.ts | 4 | 8 | 9 | 8 | **7.8** |
| utils/validate.ts | 3 | 9 | 10 | 10 | **8.5** |

## Overall Assessment

**Average Health Score: 7.9/10**

### Strengths
- Good architecture compliance across all files
- Minimal code duplication
- Clean utility functions

### Areas for Improvement
- Reduce complexity in UserForm.tsx
- Add documentation for validation rules

### Blocking Issues
None - code is ready for merge.

## Recommendations Priority

1. **High:** Refactor `validateForm` in UserForm.tsx
2. **Medium:** Extract API call to custom hook
3. **Low:** Add JSDoc comments
```

## Integration with Workflow

### Phase 6 Integration

Add to Review phase in `/auto-execute`:

```markdown
### 6.1.3 AI Code Quality Check

**Invoke ai-code-quality skill**

Run semantic analysis on all changed files:
1. Calculate quality scores
2. Generate report
3. Flag blocking issues (any score < 4)

**Blocking Criteria:**
- Overall health < 5 → BLOCK merge
- Any complexity > 8 → BLOCK merge
- Architecture compliance < 4 → BLOCK merge
```

### Automatic Invocation

This skill is automatically invoked when:
- Phase 6 (Review) is reached
- `work_type` is FRONTEND or BACKEND
- More than 3 files changed

## Thresholds Configuration

Configure in `.claude/auto-config.yaml`:

```yaml
ai_code_quality:
  enabled: true
  block_on_failure: true
  thresholds:
    complexity_max: 7
    maintainability_min: 5
    architecture_min: 6
    duplication_min: 6
    health_min: 6
  skip_patterns:
    - "*.test.ts"
    - "*.spec.ts"
    - "*.d.ts"
    - "migrations/*"
  priority_paths:
    - "src/lib/"
    - "src/api/"
    - "src/core/"
```

## Output Format

### Console Output

```
═══════════════════════════════════════════════════════════════════
 AI CODE QUALITY ANALYSIS
═══════════════════════════════════════════════════════════════════

 Files Analyzed: 3
 Average Health: 7.9/10

 ┌──────────────────────────────────────────────────────────────┐
 │ File                    │ CX │ MT │ AR │ DU │ Health │ Status│
 ├──────────────────────────────────────────────────────────────┤
 │ UserForm.tsx            │  6 │  7 │  8 │  9 │   7.5  │  ✅   │
 │ api/users.ts            │  4 │  8 │  9 │  8 │   7.8  │  ✅   │
 │ utils/validate.ts       │  3 │  9 │ 10 │ 10 │   8.5  │  ✅   │
 └──────────────────────────────────────────────────────────────┘

 Legend: CX=Complexity, MT=Maintainability, AR=Architecture, DU=Duplication

 Blocking Issues: None
 Recommendations: 3 (1 high, 1 medium, 1 low)

═══════════════════════════════════════════════════════════════════
```

### File Output

Save to `.claude/reports/code-quality-{timestamp}.md`

## Comparison with Traditional Tools

| Aspect | Linter | This Skill |
|--------|--------|------------|
| Syntax errors | ✅ | - |
| Style violations | ✅ | - |
| Cognitive complexity | ⚠️ Basic | ✅ Deep |
| Maintainability | ❌ | ✅ |
| Architecture fit | ❌ | ✅ |
| Semantic duplication | ❌ | ✅ |
| "Is this good code?" | ❌ | ✅ |

## When NOT to Use This Skill

Do NOT use this skill when:

1. **Only config files changed** - No semantic analysis needed
2. **Auto-generated code** - Prisma clients, protobuf, etc.
3. **Migrations** - SQL migrations have different standards
4. **Test files** - Test code has different patterns
5. **Urgent hotfixes** - Skip for critical production fixes

## Quality Standards

**Pass if:**
- Overall health ≥ 6
- No dimension < 4
- No blocking issues

**Warn if:**
- Any dimension 4-5
- High priority recommendations exist

**Block if:**
- Overall health < 5
- Any dimension < 4
- Critical architecture violations

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/adiomas) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
