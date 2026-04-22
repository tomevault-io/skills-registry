---
name: docs-summarize-pr
description: Generate a concise summary of a pull request Use when this capability is needed.
metadata:
  author: blahami2
---

# docs-summarize-pr

Generate a concise summary of a pull request

## Variables

- `{{diff}}` (required): The git diff to summarize
- `{{context}}`: Additional context about the PR

## Rules

- Be concise but comprehensive.
- Highlight breaking changes prominently.
- Group related changes together.
- Use bullet points for clarity.

## Prompt

Analyze this pull request and provide a clear summary:

```diff
{{diff}}
```

{{#context}}
Context: {{context}}
{{/context}}

Provide:
1. **Overview**: One-line summary of what this PR does
2. **Changes**: Bullet points of key changes
3. **Breaking Changes**: Any backward-incompatible changes (if any)
4. **Testing**: What testing was done or is needed
5. **Impact**: Areas of the codebase affected

Format as markdown suitable for a PR description.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/blahami2) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
