---
name: code-review
description: Reviews code for bugs, security vulnerabilities, and style issues. Use when the user asks for a code review, wants feedback on a snippet, or mentions reviewing code quality. Use when this capability is needed.
metadata:
  author: adamwdraper
---
# Code Review Instructions

When reviewing code, follow these steps:

## Step 1: Identify Issues
Look for:
- Bugs and logic errors
- Security vulnerabilities (SQL injection, XSS, etc.)
- Performance problems (N+1 queries, unnecessary allocations)
- Missing error handling

## Step 2: Check Style
Verify:
- Consistent naming conventions
- Proper indentation and formatting
- Clear variable and function names
- Appropriate use of comments

## Step 3: Suggest Improvements
For each issue found:
1. Explain **what** the problem is
2. Explain **why** it matters
3. Provide a **concrete fix** with a code snippet

Format your review as a numbered list of findings, ordered by severity (critical first).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/adamwdraper) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
