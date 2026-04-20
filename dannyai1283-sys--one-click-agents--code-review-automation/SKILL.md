---
name: code-review-automation
description: Automated code review assistance with PR templates, review checklists, linting integration, and security scanning. Helps ensure code quality before human review. Use when this capability is needed.
metadata:
  author: dannyai1283-sys
---

# Code Review Automation

Streamline code reviews with automated checks, PR templates, and review assistance. Ensures quality gates before human reviewers spend time.

## When to Use

- Automating repetitive review tasks
- Enforcing code quality standards
- Running security scans on PRs
- Generating review summaries
- Checking PR readiness
- Managing review assignments

## Quick Start

```bash
# Setup code review automation
./setup.sh

# Check PR readiness
openclaw review check

# Generate review summary
openclaw review summary

# Run automated checks
openclaw review lint
openclaw review security
openclaw review tests
```

## PR Templates

### Basic Template

```markdown
<!-- .github/pull_request_template.md -->
## Description
Brief description of changes

## Type of Change
- [ ] Bug fix
- [ ] New feature
- [ ] Breaking change
- [ ] Documentation update

## Testing
- [ ] Unit tests added/updated
- [ ] Manual testing performed
- [ ] All tests pass

## Checklist
- [ ] Code follows style guidelines
- [ ] Self-review completed
- [ ] Documentation updated
- [ ] No console errors
```

### Comprehensive Template

```markdown
## Summary
<!-- One-line summary -->

## Motivation
<!-- Why is this change needed? -->

## Changes
<!-- What changed? -->

## Testing
<!-- How was this tested? -->

## Screenshots
<!-- If applicable -->

## Checklist
### Code Quality
- [ ] No linting errors
- [ ] No TypeScript errors
- [ ] No console warnings
- [ ] Code is documented

### Testing
- [ ] Unit tests added
- [ ] Integration tests pass
- [ ] Manual testing done
- [ ] Edge cases handled

### Security
- [ ] No secrets in code
- [ ] Input validated
- [ ] Dependencies updated
- [ ] Security scan passed

### Review
- [ ] Self-reviewed
- [ ] Complex logic commented
- [ ] Breaking changes documented
```

## Automated Checks

### Pre-Review Checklist

```bash
#!/bin/bash
# scripts/pre-review.sh

echo "🔍 Running pre-review checks..."

# Check for console.log statements
echo "Checking for console.log..."
if grep -r "console.log" --include="*.js" --include="*.ts" src/; then
    echo "❌ Found console.log statements"
    exit 1
fi

# Check for TODO/FIXME without issue reference
echo "Checking TODOs..."
if grep -r "TODO\|FIXME" --include="*.js" --include="*.ts" src/ | grep -v "TODO(#"; then
    echo "⚠️  TODOs without issue references found"
fi

# Run linter
echo "Running linter..."
npm run lint || exit 1

# Run type checker
echo "Running type check..."
npm run typecheck || exit 1

# Run tests
echo "Running tests..."
npm test || exit 1

echo "✅ All checks passed!"
```

### Review Bot Configuration

```yaml
# .github/review-bot.yml
automated_checks:
  enabled: true
  
  linters:
    - eslint
    - prettier
    - stylelint
  
  security:
    - snyk
    - dependabot
    - codeql
  
  tests:
    required_pass: true
    coverage_threshold: 80
  
  size:
    max_files: 20
    max_lines: 500
    warning_threshold: 300
  
labels:
  size/xs:
    max_lines: 50
  size/s:
    max_lines: 100
  size/m:
    max_lines: 300
  size/l:
    max_lines: 500
  size/xl:
    max_lines: 1000
  
auto_assign:
  enabled: true
  reviewers:
    - senior-developer
    - team-lead
  
  code_owners: true
  
comment_templates:
  large_pr: |
    ⚠️ This PR is quite large ({lines} lines across {files} files).
    
    Consider breaking it down into smaller, more focused PRs:
    - One for refactoring
    - One for new features
    - One for bug fixes
  
  missing_tests: |
    🧪 No tests found in this PR.
    
    Please add tests for:
    - [ ] New functionality
    - [ ] Edge cases
    - [ ] Error handling
```

## Review Commands

### PR Analysis

```bash
# Check PR size
openclaw review size
# Output: 15 files, 340 lines (MEDIUM)

# Check test coverage
openclaw review coverage
# Output: 87% coverage (+2% from main)

# Check for security issues
openclaw review security
# Output: No issues found

# Generate review summary
openclaw review summary
# Output:
# Files changed: 15
# Test coverage: 87%
# Security scan: ✅ Pass
# Performance impact: None detected
```

### Automated Review Comments

```yaml
# .github/workflows/review.yml
name: Automated Review

on:
  pull_request:
    types: [opened, synchronize]

jobs:
  review:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0
      
      - name: Check PR size
        id: size
        run: |
          LINES=$(git diff --stat origin/main | tail -1 | awk '{print $4}')
          echo "lines=$LINES" >> $GITHUB_OUTPUT
      
      - name: Comment on large PR
        if: steps.size.outputs.lines > 500
        uses: actions/github-script@v7
        with:
          script: |
            github.rest.issues.createComment({
              issue_number: context.issue.number,
              owner: context.repo.owner,
              repo: context.repo.repo,
              body: '⚠️ This PR is quite large. Consider breaking it down.'
            })
```

## Code Quality Gates

### Branch Protection

```bash
# Configure branch protection rules
openclaw review protect main \
    --require-reviews 2 \
    --require-checks "CI,Security Scan" \
    --require-updated \
    --block-force-push
```

### Required Checks

```yaml
# .github/settings.yml
branches:
  - name: main
    protection:
      required_status_checks:
        strict: true
        contexts:
          - "CI"
          - "Lint"
          - "Security Scan"
          - "Test Coverage"
      required_pull_request_reviews:
        required_approving_review_count: 2
        dismiss_stale_reviews: true
        require_code_owner_reviews: true
      enforce_admins: true
      restrictions: null
```

## Review Assignment

### Auto-Assignment Rules

```yaml
# .github/codeowners
# Global fallback
* @team-lead

# Frontend
/src/components/ @frontend-team
/src/styles/ @frontend-team

# Backend
/src/api/ @backend-team
/src/models/ @backend-team

# Infrastructure
/terraform/ @devops-team
/.github/ @devops-team

# Documentation
/docs/ @docs-team
*.md @docs-team
```

### Review Rotation

```bash
# Configure round-robin assignment
openclaw review assign --rotation frontend --members alice,bob,carol

# Get next reviewer
openclaw review next --team frontend
# Output: alice (3 PRs this week)
```

## Security Review

### Secret Detection

```yaml
# .github/workflows/security-review.yml
name: Security Review

on:
  pull_request:
    paths:
      - '**/*'

jobs:
  security:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Secret Detection
        uses: trufflesecurity/trufflehog@main
        with:
          path: ./
          base: ${{ github.event.repository.default_branch }}
          head: HEAD
          extra_args: --debug --only-verified
      
      - name: Dependency Check
        uses: snyk/actions/node@master
        env:
          SNYK_TOKEN: ${{ secrets.SNYK_TOKEN }}
      
      - name: CodeQL Analysis
        uses: github/codeql-action/analyze@v2
```

### Vulnerability Scanning

```bash
# Scan dependencies
openclaw review security --deps

# Scan for secrets
openclaw review security --secrets

# Scan container images
openclaw review security --container

# Full security report
openclaw review security --full
```

## Performance Review

### Bundle Analysis

```bash
# Analyze bundle size
openclaw review bundle
# Output:
# Main bundle: 245KB (+12KB)
# Vendor bundle: 890KB (-5KB)
# Total: 1.13MB (+7KB)

# Check for duplicates
openclaw review bundle --duplicates

# Performance budget
openclaw review budget
# Output: ✅ All budgets passed
```

### Lighthouse CI

```yaml
# .github/workflows/lighthouse.yml
name: Lighthouse CI

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  lighthouse:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Run Lighthouse CI
        run: |
          npm install -g @lhci/cli@0.12.x
          lhci autorun
        env:
          LHCI_GITHUB_APP_TOKEN: ${{ secrets.LHCI_GITHUB_APP_TOKEN }}
```

## Review Reports

### Weekly Summary

```bash
openclaw review report --weekly
# Output:
# Week of Jan 15-21
# =================
# PRs opened: 23
# PRs merged: 19
# Avg review time: 4.2 hours
# Avg PR size: 180 lines
# 
# Top reviewers:
#   alice: 12 reviews
#   bob: 10 reviews
#   carol: 8 reviews
```

### Metrics Dashboard

```yaml
# Review metrics
metrics:
  time_to_first_review:
    target: < 2 hours
    current: 1.8 hours
  
  review_rounds:
    target: < 2
    current: 1.5
  
  approval_time:
    target: < 24 hours
    current: 18 hours
```

## Best Practices

1. **Small PRs** - Under 400 lines when possible
2. **Clear Descriptions** - Explain what and why
3. **Tests Included** - Every PR needs tests
4. **Self-Review** - Review your own PR first
5. **Timely Reviews** - Within 24 hours
6. **Constructive Feedback** - Be kind and specific
7. **Learn from Reviews** - Update guidelines based on patterns

## Integration with OpenClaw

```bash
# Agent-assisted review
openclaw agent review --pr 123

# Auto-summarize changes
openclaw agent summarize --pr 123

# Suggest reviewers
openclaw agent suggest-reviewers --pr 123
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/dannyai1283-sys) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
