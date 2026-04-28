---
name: github-issue
description: Fetch a GitHub issue and generate a plan to resolve it Use when this capability is needed.
metadata:
  author: agentuity
---

# GitHub Issue Skill

This skill helps you work on GitHub issues by fetching the issue details and generating a structured plan for resolution.

## Usage

When the user wants to work on a GitHub issue, use the `fetch-issue` tool to get the issue details:

```
fetch-issue 123
fetch-issue https://github.com/agentuity/sdk/issues/123
```

## Workflow

1. **Fetch the Issue**: Use the `fetch-issue` tool with the issue number or URL
2. **Analyze the Output**: The tool returns the issue details including title, description, labels, comments, and resolution instructions
3. **Follow the Plan**: The output includes step-by-step instructions for:
   - Understanding the issue
   - Analyzing the codebase
   - Creating an implementation plan
   - Implementing the solution
   - Testing the changes
   - Summarizing the work

## Requirements

- GitHub CLI (`gh`) must be installed and authenticated
- Run `gh auth login` if not already authenticated

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/agentuity) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
