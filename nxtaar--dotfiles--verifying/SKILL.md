---
name: verifying
description: Comprehensive verification and validation before marking work complete. Runs parallel validation for tests, security, performance, and documentation. Use when development completes, before committing, before creating PR, or when user says "verify", "validate", "check everything", "is it ready". Use when this capability is needed.
metadata:
  author: nxtaar
---

# Verifying

Multi-layer validation through parallel specialized checks with evidence gathering.

## When to Activate

- All development tasks complete
- Before creating pull request
- User requests final verification
- Pre-merge validation checkpoint
- After debugging fixes applied

## Process

### 1. Gather Verification Scope

Identify what needs validation:
```
- Changed files (git diff --name-only)
- New functionality added
- Modified interfaces/APIs
- Updated dependencies
- Configuration changes
```

### 2. Run Verification Stages

Execute in order, fail fast on critical issues:

#### Stage A: Build Verification
```bash
# Must pass before proceeding
npm run build          # or equivalent
npm run typecheck      # TypeScript projects
```

#### Stage B: Static Analysis
```bash
npm run lint           # Code style
npm audit              # Dependency vulnerabilities
```

#### Stage C: Test Execution
```bash
npm test               # Full test suite
npm run test:coverage  # Coverage report
```

#### Stage D: Manual Verification
- Smoke test critical paths
- Visual inspection (if UI changes)
- Cross-browser/device check (if applicable)

### 3. Parallel Validation Agents

Spawn specialized reviewers for comprehensive coverage:

**Test Coverage Agent:**
- Analyze coverage report
- Identify untested code paths
- Suggest missing test cases
- Check edge case coverage

**Security Review Agent:**
- Scan for hardcoded secrets
- Check input validation
- Review auth/authz paths
- Identify data exposure risks

**Code Quality Agent:**
- Check for code smells
- Review error handling
- Verify logging adequacy
- Assess maintainability

### 4. Evidence Collection

Gather proof of verification:

```markdown
## Evidence Artifacts
- [ ] Build log (success)
- [ ] Lint output (clean)
- [ ] Test results (all pass)
- [ ] Coverage report (threshold met)
- [ ] Security scan (no critical issues)
- [ ] Screenshots (if UI)
```

### 5. Generate Verification Report

Compile findings with clear recommendation.

## Output Format

```markdown
## Verification Report: [Feature/PR Name]

### Summary
| Check | Status | Details |
|-------|--------|---------|
| Build | PASS/FAIL | [notes] |
| Types | PASS/FAIL | [error count] |
| Lint | PASS/FAIL | [warning count] |
| Tests | PASS/FAIL | [X pass, Y fail] |
| Coverage | PASS/FAIL | [X%] |
| Security | PASS/WARN/FAIL | [findings] |

### Test Results
- Total: [X] tests
- Passed: [Y]
- Failed: [Z]
- Coverage: [N%]

### Critical Issues (Block Merge)
1. [Issue with location and required fix]

### Warnings (Fix Before Merge)
1. [Warning with recommendation]

### Suggestions (Optional Improvements)
1. [Nice-to-have enhancement]

### Files Changed
[List with line counts]

### Recommendation
**[READY_TO_MERGE | NEEDS_FIXES | NEEDS_REVIEW]**

[Explanation of recommendation]
```

## Agent Dispatch Templates

### Test Coverage Agent
```
Analyze test coverage for recent changes:

## Changed Files
[file list]

## Tasks
1. Run test suite, report results
2. Analyze coverage report
3. Identify untested code paths
4. Suggest 3-5 missing test cases

Focus on critical paths and edge cases.
```

### Security Review Agent
```
Security review for changes:

## Changed Files
[file list]

## Checklist
- [ ] No hardcoded credentials/secrets
- [ ] Input validation on user data
- [ ] Output encoding (XSS prevention)
- [ ] SQL injection prevention
- [ ] Authentication checks present
- [ ] Authorization verified
- [ ] Sensitive data handling
- [ ] No vulnerable dependencies

Report: SECURE | CONCERNS: [list] | CRITICAL: [list]
```

### Code Quality Agent
```
Review code quality of changes:

## Changed Files
[file list]

## Checklist
- [ ] Error handling complete
- [ ] Logging adequate
- [ ] No dead code
- [ ] Consistent patterns
- [ ] Clear naming
- [ ] Appropriate comments
- [ ] No magic numbers

Report issues by severity.
```

## Verification Commands by Stack

| Stack | Build | Lint | Test | Coverage |
|-------|-------|------|------|----------|
| Node/TS | `npm run build` | `npm run lint` | `npm test` | `npm run test:coverage` |
| Python | `python -m py_compile` | `ruff check .` | `pytest` | `pytest --cov` |
| Rust | `cargo build` | `cargo clippy` | `cargo test` | `cargo tarpaulin` |
| Go | `go build ./...` | `golangci-lint run` | `go test ./...` | `go test -cover` |

## Pre-Commit Checklist

Before marking complete:

```
- [ ] All tests pass
- [ ] No type errors
- [ ] No lint errors
- [ ] Coverage meets threshold
- [ ] No security vulnerabilities
- [ ] Documentation updated
- [ ] Commit message follows conventions
- [ ] PR description complete
```

## Collaboration

- If verification fails, invoke **debugging** skill
- If new requirements emerge, return to **brainstorming**
- After verification passes, proceed to commit/PR

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/nxtaar) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
