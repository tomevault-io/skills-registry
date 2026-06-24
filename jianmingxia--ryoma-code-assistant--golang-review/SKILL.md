---
name: golang-review
description: Use when reviewing Go code changes, checking Go pull requests, or validating modified .go files before commit or merge for idioms, concurrency, errors, security, and static analysis issues
metadata:
  author: JianmingXia
---

# Go Code Review

Use this skill as the source of truth for comprehensive Go-specific code review. Agents such as `golang-reviewer` should apply this policy rather than duplicating it.

## What This Skill Does

1. **Identify Go Changes**: Find modified `.go` files via `git diff`.
2. **Run Static Analysis**: Execute `go vet`, `staticcheck`, and `golangci-lint` when available.
3. **Security Scan**: Check SQL injection, command injection, path traversal, race conditions, unsafe usage, insecure TLS, and hardcoded secrets.
4. **Concurrency Review**: Analyze goroutine safety, channel usage, mutex patterns, and shared mutable state.
5. **Idiomatic Go Check**: Verify Go conventions, error wrapping, context propagation, testing patterns, and performance basics.
6. **Generate Report**: Categorize issues by severity.

## When to Use

Use this skill when:

- After writing or modifying Go code
- Before committing Go changes
- Reviewing pull requests with Go code
- Onboarding to a new Go codebase
- Learning idiomatic Go patterns

## Core Workflow

1. Identify staged and unstaged Go changes:
   ```bash
   git diff -- '*.go'
   git diff --cached -- '*.go'
   ```
2. Run available diagnostics:
   ```bash
   go vet ./...
   staticcheck ./...
   golangci-lint run
   go build -race ./...
   go test -race ./...
   govulncheck ./...
   ```
3. If optional tools are not installed, report them clearly and continue with available checks.
4. Review modified `.go` files first, then nearby code needed to understand impact.
5. Report findings with severity, file path, line number, impact, and fix direction.

## Review Categories

### CRITICAL (Must Fix)

- SQL or command injection vulnerabilities
- Path traversal with user-controlled file paths
- Race conditions without synchronization
- Goroutine leaks
- Hardcoded credentials
- Unsafe pointer usage without justification
- Insecure TLS configuration
- Ignored errors in critical paths

### HIGH (Should Fix)

- Missing error wrapping with context
- Panic instead of error returns for recoverable errors
- Context not propagated
- Unbuffered channels causing deadlocks
- Missing goroutine coordination
- Missing mutex protection
- Interface not satisfied errors

### MEDIUM (Consider)

- Non-idiomatic code patterns
- Large functions or deep nesting
- Missing godoc comments on exports
- Inefficient string concatenation
- Slice not preallocated
- Table-driven tests not used
- Deferred call in loop causing resource accumulation

## Report Format

```markdown
# Go Code Review Report

## Files Reviewed
- path/to/file.go (modified)

## Static Analysis Results
- go vet: pass/fail/not run
- staticcheck: pass/fail/not installed
- golangci-lint: pass/fail/not installed
- go build -race: pass/fail/not run
- go test -race: pass/fail/not run
- govulncheck: pass/fail/not installed

## Issues Found

[SEVERITY] Title
File: path/to/file.go:line
Issue: What is wrong and why it matters.
Fix: Specific recommended change.

## Summary
- CRITICAL: 0
- HIGH: 0
- MEDIUM: 0

Recommendation: PASS / WARNING / FAIL
```

## Approval Criteria

| Status | Condition |
| --- | --- |
| PASS: Approve | No CRITICAL or HIGH issues |
| WARNING: Warning | Only MEDIUM issues |
| FAIL: Block | CRITICAL or HIGH issues found |

## Related

- Executor agent: `agents/golang-reviewer.md`
- Referenced skills: `golang-patterns`, `golang-testing`

---
> Source: [JianmingXia/ryoma-code-assistant](https://github.com/JianmingXia/ryoma-code-assistant) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
