---
name: documentation-skill
description: Use this skill for local documentation tasks that need clear scope, consistent writing rules, and a stable final report.
metadata:
  author: Podcast72
---

# Documentation Skill

## What this skill does

Use this skill for documentation-focused tasks such as:

- writing or rewriting `README.md`;
- updating guides or internal notes stored in the repository;
- improving structure, clarity, headings, examples, or usage steps;
- keeping a final report consistent across repeated documentation tasks.

## Activation

Activate this skill when the task is primarily about documentation, explanations, or Markdown structure.

## Scope

- Local repository documentation only.
- Small to medium documentation edits.
- No network access by default.
- No global configuration changes.
- No destructive commands.

## Rules

- Read the existing document before rewriting it.
- Preserve the technical meaning unless the task explicitly asks for a change.
- Prefer short, clear sections over long narrative text.
- Keep examples small and practical.
- Report what changed and what was not checked.

## Stop rules

- Stop if the task requires changes outside the approved documentation scope.
- Stop if the request becomes ambiguous about audience, tone, or target files.
- Stop before adding claims that cannot be supported by the repository content.

## Optional template

Use `templates/final-report.md` when you want a consistent completion summary.

## Final report format

```text
MODE:
TASK:
FILES MODIFIED:
WRITING GOALS:
VALIDATION:
LIMITS:
STATUS:
```

---
> Source: [Podcast72/agent-skill-starter-kit](https://github.com/Podcast72/agent-skill-starter-kit) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-22 -->
