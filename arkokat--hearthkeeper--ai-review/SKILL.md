---
name: ai-review
description: Review Gemini API agent code against mandatory rules in backend/AGENTS.md Use when this capability is needed.
metadata:
  author: Arkokat
---

## What I do

1. Read `backend/AGENTS.md` to get the mandatory rules for Gemini API
2. Review the changed or new files that contain Gemini API agent code
3. Check for:
   - Rate limiting (10/hour per user for AI endpoints)
   - Proper structured output schema handling
   - Error handling and retry logic
   - Correct client initialization
   - No hardcoded API keys
4. Report any violations with specific line references

## When to use me

Use this after creating or modifying any Gemini API agent code. This should be run as part of the code review process.

## Important notes

- The rules in `backend/AGENTS.md` are mandatory for all AI agent implementations
- Focus on the specific rules in that file - don't make up additional requirements
- Provide actionable feedback with file:line references for any issues found

---
> Source: [Arkokat/hearthkeeper](https://github.com/Arkokat/hearthkeeper) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-23 -->
