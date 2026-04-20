---
name: find-skills
description: Discover and install agent skills from the skills.sh ecosystem and GitHub. Use when users ask for new capabilities, tools, or want to extend the agent. Use when this capability is needed.
metadata:
  author: alfredzhang98
---

# Find Skills

Search and install skills from the open agent skills ecosystem.

## When to Use

- User asks "find a skill for X" or "is there a skill for X"
- User wants to extend agent capabilities
- User asks "can you do X" where X might be an installable skill
- User mentions a specific domain (react, testing, deployment, etc.)

## Search Methods

### 1. Admin Marketplace (recommended)

The admin console at **Skills > Marketplace** has built-in search that queries:
- **skills.sh** (Vercel Labs registry) — primary source
- **GitHub** — fallback, searches for repos with SKILL.md files

### 2. CLI Search

If the skills CLI is available:

```bash
npx skills find [query]
```

Examples:
- `npx skills find react performance`
- `npx skills find pr review`
- `npx skills find changelog`

### 3. Install from CLI

```bash
npx skills add <owner/repo@skill> -g -y
```

## Common Skill Categories

| Category | Search Terms |
|----------|-------------|
| Web Dev | react, nextjs, typescript, tailwind |
| Testing | testing, jest, playwright, e2e |
| DevOps | deploy, docker, kubernetes, ci-cd |
| Docs | docs, readme, changelog, api-docs |
| Code Quality | review, lint, refactor, best-practices |
| Design | ui, ux, design-system, accessibility |

## Browse

- **skills.sh**: https://skills.sh/
- **Vercel skills**: https://github.com/vercel-labs/agent-skills
- **Community skills**: https://github.com/ComposioHQ/awesome-claude-skills

## When No Skills Found

1. Offer to help with the task directly
2. Suggest creating a custom skill: `npx skills init my-skill`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/alfredzhang98) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
