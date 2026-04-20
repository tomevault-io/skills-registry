---
name: code-reviewer
description: Review code changes, find bugs, risks, regressions, and missing tests. Use when this capability is needed.
metadata:
  author: zeroz-lab
---

# Code Reviewer Skill

## When to use
- The user asks for a code review, PR review, or to find bugs/regressions.
- The user requests test coverage advice for recent changes.

## Workflow
1. Identify review scope (diff or file list). If unclear, ask for file paths.
2. Read relevant files before judging behavior.
3. Prioritize correctness, security, and regressions.
4. Capture issues with severity and file/line references.
5. Suggest tests or checks to reduce risk.

## Output format
Findings:
- [Critical|High|Medium|Low] path:line - issue summary + impact

Questions:
- Any missing context or assumptions

Suggested tests:
- Specific tests or commands to validate behavior

## Notes
- Be concise and actionable.
- Avoid rewriting large code blocks unless requested.
- Prefer asking for context instead of guessing.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/zeroz-lab) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
