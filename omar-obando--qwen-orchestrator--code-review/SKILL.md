---
name: code-review
description: > Use when this capability is needed.
metadata:
  author: Omar-Obando
---

# Code Review Skill — Professional Quality Audit

This skill provides a systematic, evidence-based code review methodology that catches real issues, not style nits.

## Review Dimensions

### 1. Correctness (Priority: CRITICAL)

- Does the code do what it claims?
- Are edge cases handled?
- Are off-by-one errors possible?
- Are null/undefined checks present where needed?
- Are error conditions properly handled?

### 2. Security (Priority: CRITICAL)

- **Injection**: SQL, XSS, command injection, path traversal
- **Auth**: Proper authentication and authorization checks
- **Data Exposure**: No secrets in logs, error messages, or responses
- **Input Validation**: All external inputs validated and sanitized
- **Dependencies**: Known vulnerabilities in third-party packages

### 3. Performance (Priority: HIGH)

- Unnecessary allocations in hot paths
- O(n²) or worse algorithms where O(n) or O(log n) is possible
- Missing pagination for large datasets
- Synchronous operations that should be async
- Memory leaks (event listeners, subscriptions not cleaned up)

### 4. Maintainability (Priority: HIGH)

- Functions under 40 lines
- Cyclomatic complexity ≤ 10
- Meaningful names that reveal intent
- No magic numbers or strings
- Proper error types with context

### 5. Testing (Priority: HIGH)

- Tests exist for new behavior
- Tests cover happy path AND failure modes
- Tests are isolated and repeatable
- Test names describe expected behavior
- No test interdependencies

### 6. Architecture (Priority: MEDIUM)

- Proper separation of concerns
- No circular dependencies
- Dependency direction is correct (inward in hexagonal)
- No layer violations
- Single Responsibility Principle followed

## Severity Classification

| Severity | Criteria                               | Action                  |
| -------- | -------------------------------------- | ----------------------- |
| BLOCKER  | Security vulnerability, data loss risk | Must fix before merge   |
| CRITICAL | Bug, incorrect behavior                | Must fix before merge   |
| MAJOR    | Performance issue, poor error handling | Should fix before merge |
| MINOR    | Style, naming, minor improvement       | Can fix later           |
| INFO     | Suggestion, alternative approach       | Optional                |

## Review Workflow

1. **Read the PR description** — understand intent
2. **Read the changed files** — understand implementation
3. **Trace data flow** — from input to output
4. **Check tests** — do they verify the behavior?
5. **Check dependencies** — any side effects?
6. **Classify findings** — by severity with evidence
7. **Write review** — constructive, specific, actionable

## Output Format

```markdown
## Code Review Report

### Summary

- Files reviewed: [N]
- Findings: [BLOCKER: N] [CRITICAL: N] [MAJOR: N] [MINOR: N] [INFO: N]
- Verdict: APPROVE | REQUEST CHANGES | BLOCK

### Findings

#### [SEVERITY] [File:Line] — [Title]

**Issue**: [Description of the problem]
**Evidence**: [Code snippet showing the issue]
**Recommendation**: [Specific fix suggestion]
**Confidence**: HIGH | MEDIUM | LOW

## When NOT to Use

**Do NOT use this skill when:**

- Designing database schemas (use database-design skill for normalization and indexing)
- Writing API endpoint implementations (use api-design skill for REST/GraphQL endpoint patterns)
- Performing security audits (use security-auditor skill for comprehensive vulnerability analysis)
- **Analyzing deployment configurations** (use deployment skill for CI/CD pipelines and infrastructure)
- **Writing SQL queries** (use sql-best-practices skill for query optimization and N+1 prevention)
- **Reviewing git workflows** (use git-workflow skill for branching strategies and commit conventions)
- **Designing multi-page website layouts** (use design-system skill for professional UI/UX and spacing)
- **Implementing new features** (use domain-driven skill for complete business modules)
- **Refactoring existing code** (use refactoring skill for safe behavior-preserving transformations)
```

---
> Source: [Omar-Obando/qwen-orchestrator](https://github.com/Omar-Obando/qwen-orchestrator) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
