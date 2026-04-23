---
name: explore
description: | Use when this capability is needed.
metadata:
  author: darkroomengineering
---

# Codebase Exploration

Delegates to the Explore agent for fast, read-only investigation of the codebase.

## Current State
- Directory: !`pwd 2>/dev/null`
- Project: !`basename "$(pwd)" 2>/dev/null`
- Stack: !`ls package.json Cargo.toml go.mod pyproject.toml 2>/dev/null || echo "unknown"`
- Git root: !`git rev-parse --show-toplevel 2>/dev/null || echo "not a git repo"`

## Strategy

1. **Start broad** - Use `tldr semantic` or `Glob` to find relevant files
2. **Narrow down** - Read specific files to understand implementation
3. **Trace connections** - Use `tldr impact` to find callers/dependencies
4. **Summarize findings** - Return clear, actionable summary

## Output Format

Return a concise summary:
- **Location**: Key files and their paths
- **How it works**: Brief explanation of the flow
- **Key functions/components**: Entry points
- **Dependencies**: What it relies on
- **Suggestions**: If the user needs to modify something

## Remember

- You are READ-ONLY - do not modify files
- Return summaries, not raw file contents
- Be specific with file paths and line numbers

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/darkroomengineering) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
