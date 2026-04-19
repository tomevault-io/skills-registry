---
name: pr-code-reviewer
description: Comprehensive code review with 4 parallel subagents, smart tracing, and iterative refinement. Reviews logic, security, performance, and quality. Use when this capability is needed.
metadata:
  author: kokorolx
---

# PR Code Reviewer

## Overview
Comprehensive PR reviewer that gathers full context, applies smart tracing based on change type, runs four specialized subagents in parallel, and iteratively refines findings into a final report.

## Workflow (CRITICAL - Follow Exactly)

### Phase 1: Context Gathering
1. Get PR metadata (title, description, author, base branch)
2. Get list of changed files with diff
3. Classify change type for each file:
   - LOGIC: Conditionals, business rules, state changes -> DEEP TRACE
   - STYLE: Formatting, naming, comments -> SHALLOW TRACE
   - REFACTOR: Structure changes without logic change -> MEDIUM TRACE
   - NEW: New files -> FULL REVIEW
4. Gather full context for each changed file:
   - Related callers/callees
   - Tests covering the change
   - Types/interfaces touched
   - Usage sites (callers and public API entry points)

### Phase 2: Smart Tracing (Based on Change Type)

#### For LOGIC changes (e.g., if-else condition changed):
1. Find ALL callers of the modified function (use LSP find_references)
2. Find ALL callees (functions called by modified code)
3. Find ALL tests that cover this code path
4. Find ALL types/interfaces affected
5. Trace data flow: where does input come from? where does output go?

#### For STYLE changes:
1. Check consistency with project conventions
2. Verify no accidental logic changes hidden in style changes

#### For REFACTOR changes:
1. Verify behavior preservation
2. Check all usages still work

### Phase 3: Parallel Subagent Execution

Launch ALL 4 subagents simultaneously with run_in_background: true:

```javascript
// Subagent 1: EXPLORE - Code Quality & Patterns
delegate_task({
  subagent_type: "explore",
  load_skills: [],
  run_in_background: true,
  prompt: `
    CONTEXT: [paste PR context, changed files, traced dependencies]
    
    TASK: Analyze code quality and patterns
    
    CHECK:
    1. Code style consistency with project conventions
    2. Naming conventions (check AGENTS.md, .eslintrc, etc.)
    3. Error handling patterns
    4. Type safety (no any, proper null checks)
    5. Code duplication
    6. Complexity (deep nesting, long functions)
    
    RETURN: JSON with findings array, each with {file, line, severity, category, message, suggestion}
  `
});

// Subagent 2: ORACLE - Security & Logic
delegate_task({
  subagent_type: "oracle",
  load_skills: [],
  run_in_background: true,
  prompt: `
    CONTEXT: [paste PR context, changed files, traced dependencies]
    
    TASK: Deep security and logic analysis
    
    CHECK:
    1. OWASP Top 10 vulnerabilities
    2. Logic correctness (especially for LOGIC-type changes)
    3. Edge cases and boundary conditions
    4. Race conditions and state management
    5. Input validation
    6. Authentication/authorization
    
    CRITICAL: For any if-else or conditional changes, trace ALL code paths affected.
    
    RETURN: JSON with findings array
  `
});

// Subagent 3: LIBRARIAN - Documentation & Best Practices
delegate_task({
  subagent_type: "librarian",
  load_skills: [],
  run_in_background: true,
  prompt: `
    CONTEXT: [paste PR context, changed files]
    
    TASK: Documentation and best practices review
    
    CHECK:
    1. Are complex algorithms documented?
    2. Are public APIs documented (JSDoc/TypeDoc)?
    3. Are non-obvious decisions explained?
    4. Do changes follow framework best practices? (Next.js, React, etc.)
    5. Are there any anti-patterns from official docs?
    
    RETURN: JSON with findings array
  `
});

// Subagent 4: GENERAL - Tests & Integration
delegate_task({
  subagent_type: "general",
  load_skills: [],
  run_in_background: true,
  prompt: `
    CONTEXT: [paste PR context, changed files, traced test files]
    
    TASK: Test coverage and integration analysis
    
    CHECK:
    1. Are new code paths tested?
    2. Are edge cases covered?
    3. Are existing tests still valid after changes?
    4. Integration points - do changes break contracts?
    5. Performance implications
    
    RETURN: JSON with findings array
  `
});
```

### Phase 4: Iterative Refinement

After collecting all subagent results:

1. **First Pass Synthesis**:
   - Merge all findings
   - Deduplicate (same issue found by multiple agents)
   - Categorize by severity

2. **Gap Analysis**:
   - Did any subagent fail or return incomplete results?
   - Are there untested code paths?
   - Are there unreviewed files?

3. **Second Pass** (if gaps found):
   - Launch targeted follow-up queries
   - Focus on identified gaps

4. **Final Report Generation**

### Phase 5: Report Generation

Save to `~/pr-reviews/{owner}_{repo}_PR{number}_{timestamp}.md`

```markdown
# Code Review Report

## Summary
| Metric | Value |
|--------|-------|
| PR | #{number} |
| Repository | {owner}/{repo} |
| Files Changed | {count} |
| Critical Issues | {count} |
| Warnings | {count} |
| Suggestions | {count} |

## Change Classification
| File | Type | Trace Depth |
|------|------|-------------|
| src/auth.ts | LOGIC | DEEP |
| src/styles.css | STYLE | SHALLOW |

## Critical Issues (MUST FIX)
### 1. [Title]
- **File**: path:line
- **Severity**: CRITICAL
- **Category**: Security/Logic/Performance
- **Issue**: Description
- **Impact**: What could go wrong
- **Fix**: Suggested solution

## Warnings (SHOULD FIX)
[Same structure]

## Suggestions (CONSIDER)
[Same structure]

## Praise (GOOD WORK)
[Highlight positive patterns]

## Traced Dependencies
[List of all files/functions that were traced due to LOGIC changes]

## Test Coverage Analysis
[Which tests cover the changed code]

## Recommendation
- [ ] APPROVE - No critical issues
- [ ] REQUEST CHANGES - Critical issues found
- [ ] COMMENT - Suggestions only
```

## Configuration

Check for config at `.opencode/code-reviewer.json`:

```json
{
  "output": {
    "local": true,
    "github_comment": false,
    "github_review": false
  },
  "trace_depth": {
    "logic": "deep",
    "style": "shallow",
    "refactor": "medium"
  },
  "ignore_patterns": [
    "*.test.ts",
    "*.spec.ts",
    "__mocks__/**"
  ]
}
```

## Usage Examples

```
/review PR#123
/review https://github.com/owner/repo/pull/123
/review --staged  (review staged changes locally)
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kokorolx) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
