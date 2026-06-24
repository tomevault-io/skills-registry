---
name: code-review
description: Performs structured code reviews focusing on bugs, security, performance, and best practices. Use when reviewing code, pull requests, diffs, or when the user asks for feedback on implementations. Use when this capability is needed.
metadata:
  author: cambridge-ai-build-club
---

# Code Review

## Quick Start

When reviewing code, follow this structured approach:

```
Review Checklist:
- [ ] Correctness: Does it work as intended?
- [ ] Security: Any vulnerabilities or data exposure?
- [ ] Performance: Obvious inefficiencies?
- [ ] Readability: Clear naming, structure, comments?
- [ ] Edge cases: Null, empty, boundary conditions?
```

## Review Process

**Step 1: Understand Context**
- What problem does this code solve?
- What are the requirements/constraints?

**Step 2: Scan for Critical Issues**
- Security vulnerabilities (injection, auth, data exposure)
- Logic errors and bugs
- Resource leaks or performance bombs

**Step 3: Evaluate Quality**
- Code organization and structure
- Naming conventions
- Error handling patterns
- Test coverage (if applicable)

**Step 4: Provide Feedback**
- Lead with positives
- Categorize issues: Critical / Important / Suggestion
- Include specific line references
- Offer concrete alternatives

## Output Format

Structure feedback as:

```markdown
## Summary
[1-2 sentence overview]

## Critical Issues
[Must fix before merge]

## Recommendations  
[Should address]

## Suggestions
[Nice to have improvements]

## What's Working Well
[Positive observations]
```

## Language-Specific Guidance

For detailed patterns by language, see:
- [PATTERNS.md](PATTERNS.md) - Common anti-patterns by language
- [SECURITY.md](SECURITY.md) - Security checklist by context

## Review Scope Guidelines

| Review Type | Focus Areas | Depth |
|-------------|-------------|-------|
| Quick review | Bugs, security | Surface |
| Standard review | + Performance, readability | Thorough |
| Deep review | + Architecture, patterns | Comprehensive |

Default to **standard review** unless specified otherwise.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/cambridge-ai-build-club) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
