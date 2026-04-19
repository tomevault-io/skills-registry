---
name: code-review
description: Performs comprehensive code reviews checking for security vulnerabilities, performance issues, style violations, team conventions, language patterns, dependency freshness, best practices, and code duplication. Use when reviewing code changes, PRs, or files. Use when this capability is needed.
metadata:
  author: davidbrownell
---

# Comprehensive Code Review

Perform a thorough code review of the specified code, checking for security, performance, style, conventions, and best practices.

## Input

The target for review is provided as: `$ARGUMENTS`

Accepted inputs:
- **File path**: Review a specific file (e.g., `src/utils/auth.ts`) or directory containing source files
- **PR number**: Review a pull request (e.g., `#123` or `123`)
- **"staged"**: Review all currently staged git changes
- **"diff"**: Review unstaged changes in the working directory
- **"all"**: All code within the repository
- **No argument**: Ask the user what they want reviewed

## Review Categories

Analyze the code against each of these categories and report findings:

### 1. Security Vulnerabilities

Check for:
- Injection vulnerabilities (SQL, command, XSS, LDAP, etc.)
- Authentication and authorization flaws
- Sensitive data exposure (hardcoded secrets, credentials, API keys)
- Insecure cryptographic practices
- Missing input validation and sanitization
- Insecure deserialization
- Path traversal vulnerabilities
- CSRF vulnerabilities
- Improper error handling that leaks information
- OWASP Top 10 violations

### 2. Performance Issues

Check for:
- Inefficient algorithms (O(n²) when O(n) is possible, etc.)
- N+1 query problems
- Missing database indexes (when schema is available)
- Unnecessary re-renders (React, Vue, etc.)
- Memory leaks and resource cleanup issues
- Blocking operations in async contexts
- Excessive memory allocation
- Missing caching opportunities
- Inefficient data structures
- Unoptimized loops and iterations

### 3. Style Violations

Check for:
- Inconsistent naming conventions
- Improper indentation and formatting
- Missing or excessive whitespace
- Line length violations
- Inconsistent brace/bracket style
- Import ordering issues
- Commented-out code that should be removed
- TODO/FIXME comments that need addressing
- Compliance with project linter rules (if config files exist)

### 4. Team Conventions

Look for project-specific patterns by examining:
- Existing code in the repository for established patterns
- Configuration files (.eslintrc, .prettierrc, pyproject.toml, etc.)
- Contributing guidelines (CONTRIBUTING.md)
- Existing documentation
- Test patterns and naming conventions
- File and folder organization patterns
- Error handling patterns used elsewhere in codebase

Flag deviations from established team patterns.

### 5. Language Canonical Patterns

Check adherence to idiomatic patterns for the source language:
- **TypeScript/JavaScript**: Proper typing, async/await usage, module patterns
- **Python**: PEP 8, Pythonic idioms, type hints
- **Go**: Effective Go guidelines, error handling patterns
- **Rust**: Ownership patterns, Result/Option usage
- **Java**: Java conventions, design patterns
- **C#**: .NET conventions, LINQ usage
- (Apply similar standards for other languages)

### 6. Dependency Freshness

When new dependencies are added:
- Check if the dependency is actively maintained
- Verify the version is current (not outdated)
- Look for known vulnerabilities in the version
- Assess if the dependency is necessary or if native solutions exist
- Check license compatibility
- Evaluate the dependency's size and impact on bundle

### 7. Industry Best Practices

Check for:
- SOLID principles violations
- DRY (Don't Repeat Yourself) violations
- Proper separation of concerns
- Appropriate error handling and logging
- Meaningful variable and function names
- Functions doing one thing well
- Appropriate abstraction levels
- Proper use of design patterns
- Documentation for public APIs
- Test coverage for new functionality

### 8. Code Duplication (DRY)

Scan for:
- Repeated code blocks within the reviewed files
- Similar logic that exists elsewhere in the codebase
- Copy-pasted code with minor variations
- Opportunities to extract common utilities
- Duplicated algorithms that could be consolidated
- Repeated patterns that warrant abstraction

## Output Format

Structure your review as follows:

```markdown
# Code Review: [target]

## Summary
[Brief 2-3 sentence overview of the code quality and main findings]

## Critical Issues 🔴
[Security vulnerabilities and bugs that must be fixed]

## Warnings 🟡
[Performance issues, potential bugs, and significant best practice violations]

## Suggestions 🔵
[Style improvements, minor optimizations, and nice-to-haves]

## Detailed Findings

### Security
[Findings or "No issues found"]

### Performance
[Findings or "No issues found"]

### Style
[Findings or "No issues found"]

### Team Conventions
[Findings or "No issues found"]

### Language Patterns
[Findings or "No issues found"]

### Dependencies
[Findings or "N/A - No new dependencies"]

### Best Practices
[Findings or "No issues found"]

### Code Duplication
[Findings or "No duplication detected"]

## Positive Observations ✅
[Note good practices and well-written code sections]
```

## Severity Levels

- **🔴 Critical**: Must fix before merge (security issues, bugs, broken functionality)
- **🟡 Warning**: Should fix (performance, maintainability, potential issues)
- **🔵 Suggestion**: Consider fixing (style, minor improvements)

## Instructions

1. If reviewing a file, read the file first
2. If reviewing a PR, use `gh pr diff [number]` to get the changes
3. If reviewing staged changes, use `git diff --cached`
4. If reviewing unstaged changes, use `git diff`
5. For context, examine related files in the codebase
6. Search for duplicated code patterns across the repository
7. Check for existing linter/formatter configurations
8. Provide specific line numbers for each finding
9. Include code snippets showing the issue and suggested fix
10. Be constructive and educational in feedback

## Edge Cases

- **Binary files**: Skip with note that binary files cannot be reviewed
- **Generated files**: Note if file appears generated and skip detailed review
- **Empty diff**: Report that there are no changes to review
- **Invalid file path**: Ask user to provide a valid path
- **PR not found**: Report error and ask for correct PR number

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/davidbrownell) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
