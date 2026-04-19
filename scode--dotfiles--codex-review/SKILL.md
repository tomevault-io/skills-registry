---
name: codex-review
description: Have OpenAI's Codex CLI review the current changes Use when this capability is needed.
metadata:
  author: scode
---

# Codex Code Review

Dispatch the `codex-code-review:codex-code-review` agent to review current changes using OpenAI's Codex CLI.

## Instructions

Use the Task tool to dispatch the `codex-code-review:codex-code-review` subagent with the following prompt:

```
Review the current changes. {ADDITIONAL_CONTEXT}
```

Where `{ADDITIONAL_CONTEXT}` is any context the user provided when invoking the skill (e.g.,
`/codex-review focus on error handling`).

If no additional context was provided, simply ask the agent to review the current changes.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/scode) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
