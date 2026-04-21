---
name: find-skills
description: Helps users discover and install agent skills when they ask about specialized capabilities or workflows. Use when this capability is needed.
metadata:
  author: mfuentesg
---

# Find Skills

This skill helps you discover and install skills from the open agent skills ecosystem.

## When to Use

Use this skill when the user:

- Asks for help with a specialized task
- Wants to search for tools or workflows
- Asks if a skill exists for a domain

## Skills CLI

- `npx skills find [query]`
- `npx skills add <owner/repo@skill>`
- `npx skills check`
- `npx skills update`

Browse skills at https://skills.sh/

## Example

```
I found a skill that might help. The "vercel-react-best-practices" skill provides
React and Next.js performance optimization guidelines.

To install it:
  npx skills add vercel-labs/agent-skills@vercel-react-best-practices

Learn more:
  https://skills.sh/vercel-labs/agent-skills/vercel-react-best-practices
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mfuentesg) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
