---
name: analyze
description: Analyze an open source project's structure, conventions, and contribution guidelines. Use when user wants to understand a project before contributing. Use when this capability is needed.
metadata:
  author: jay05410
---

# Project Analysis Skill

Analyze an open source project to understand its contribution requirements.

## When to Use

- User mentions wanting to contribute to a project
- User asks about a project's contribution guidelines
- User wants to understand a codebase structure
- User provides a GitHub repository URL

## Process

1. **Identify the repository**
   - Parse GitHub URL or repo name (owner/repo format)
   - Use GitHub MCP to fetch repository metadata

2. **Scan contribution guidelines**
   - CONTRIBUTING.md / CONTRIBUTE.md
   - .github/CONTRIBUTING.md
   - CODE_OF_CONDUCT.md
   - README.md (Contributing section)

3. **Detect conventions**
   - Check for issue/PR templates in .github/
   - Analyze commit history for message conventions
   - Identify code style configs (.editorconfig, .prettierrc, .eslintrc, etc.)

4. **Identify entry points**
   - Search for "good first issue" labeled issues
   - Check for "help wanted" labels
   - Look for documentation contribution opportunities

## Output Format

Provide analysis in the user's language:

```
# Project Analysis: [Name]

## Overview
- Language: [primary language]
- License: [license]
- Stars: [count]
- Activity: [last commit date, issue response time]

## Contribution Requirements
- [ ] CLA required: [Yes/No]
- [ ] Issue first: [Yes/No]
- [ ] Tests required: [Yes/No]

## Conventions
- Branch naming: [pattern]
- Commit format: [convention]
- PR template: [exists/not]

## Getting Started
[Quick start commands for the project]

## Recommended Entry Points
- Good first issues: [count]
- Help wanted: [count]
```

## Arguments

`$ARGUMENTS` can be:
- GitHub URL: `https://github.com/owner/repo`
- Short format: `owner/repo`
- Project name: `react` (will search)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jay05410) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
