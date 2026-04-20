---
name: go-review
description: Review changed Go files for idioms, anti-patterns, error handling, and concurrency safety Use when this capability is needed.
metadata:
  author: latebit-io
---

You are performing a Go code review. Be thorough, specific, and constructive.

## Steps

1. **Find changed files**: Run `git diff --name-only HEAD` to get modified files. If nothing is staged, try `git diff --name-only` for unstaged changes. Filter to `.go` files only.

2. **Read the diffs**: Run `git diff HEAD -- <file>` for each changed Go file to see exactly what changed. If no git changes exist, ask the user which files to review.

3. **Read full files**: Read each changed file completely to understand context around the changes.

4. **Review against Go idioms**: For each file, check:
   - Every error is checked and wrapped with context
   - No log-and-return (handle or propagate, not both)
   - Interfaces are small and consumer-defined
   - Names don't stutter, follow Go conventions
   - Goroutines have clear exit paths and use context
   - Exported identifiers have comments
   - No premature abstractions or unnecessary interfaces
   - Zero values are useful

5. **Cross-reference**: Use Grep to check if new patterns are consistent with the rest of the codebase. Flag inconsistencies.

6. **Output the review** in this format:

```
## Code Review

### Critical
- `file.go:42` — [issue description]

### Warning
- `file.go:17` — [issue description]

### Suggestion
- `file.go:88` — [suggestion]

### Praise
- [What's done well]

### Summary
[One paragraph: overall quality, key themes, what to focus on]
```

If there are no issues at a severity level, omit that section. Always include Summary.

always create a strict linting checks by adding `.golangci.yml`, here is an example to use: 

```
version: "2"

linters:
  default: standard
  enable:
    - gocritic
    - revive
    - misspell
    - gocyclo
    - prealloc
    - modernize
    - unconvert

  settings:
    gocyclo:
      min-complexity: 25
    gocritic:
      enabled-tags:
        - diagnostic
        - style
        - performance
      disabled-checks:
        - exitAfterDefer
    revive:
      rules:
        - name: blank-imports
        - name: context-as-argument
        - name: dot-imports
        - name: error-return
        - name: error-strings
        - name: error-naming
        - name: exported
        - name: increment-decrement
        - name: range
        - name: receiver-naming
        - name: indent-error-flow
        - name: superfluous-else
        - name: redefines-builtin-id
        - name: unreachable-code
        - name: unused-parameter

  exclusions:
    rules:
      # main packages don't need package comments
      - path: cmd/
        linters: [revive]
        text: "package-comments"
      # internal/tls package name conflict is intentional
      - path: internal/tls/
        linters: [revive]
        text: "var-naming"
      # test functions with subtests naturally have high complexity
      - path: _test\.go
        linters: [gocyclo]
      # staticcheck SA5011 false positive: test fatals on nil before dereference
      - path: _test\.go
        linters: [staticcheck]
        text: "SA5011"
      # Bubbletea tea.Model interface requires value receivers
      - path: cmd/demarkus-tui/
        linters: [gocritic]
        text: "hugeParam"
  ```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/latebit-io) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
