---
name: codex-review
description: Request an independent senior-level peer review from Codex, then implement warranted feedback. Use when this capability is needed.
metadata:
  author: thecrazybob
---

You are requesting a peer review from Codex, and then implementing the feedback and suggestions IF you feel they are worthy and valid.

## Workflow

### Step 1: Send to Codex for review

Use the `codex review` subcommand, which automatically inspects staged changes:

```bash
codex review "Review as an expert in Laravel, PHP, Vue.js, Inertia.js, and Tailwind CSS. Confirm that our guidelines are properly being followed. Provide: 1) Critical Issues - bugs, security vulnerabilities, or breaking changes that MUST be fixed. 2) Improvements - concrete suggestions for better code quality, performance, or maintainability. 3) Nitpicks - minor style or preference items (low priority). Be specific. Reference exact lines from the diff. Provide code examples for suggested fixes. Do NOT comment on formatting or whitespace. Do NOT suggest adding comments or docblocks unless something is genuinely confusing. Keep your review concise and actionable."
```

### Step 2: Present the feedback

Display Codex's full review to the user in a clear, formatted way.

### Step 3: Evaluate and implement

Run `git diff --cached` yourself so you have full context, then evaluate each piece of Codex's feedback:

- **Implement** any suggestions that are clearly correct improvements (bugs, security issues, genuine code quality wins).
- **Ignore** anything that is purely stylistic preference, overly cautious, or conflicts with the project's established patterns and conventions. You have greater context about this codebase than Codex does.
- **Explain** briefly which suggestions you're implementing and which you're skipping (and why).

After making changes, run `vendor/bin/pint --dirty` to fix formatting on changed files.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/thecrazybob) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
