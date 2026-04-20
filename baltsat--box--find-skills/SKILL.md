---
name: find-skills
description: TRIGGER when: user explicitly asks to find, discover, or install a skill — e.g. 'find a skill for X', 'is there a skill that can...', 'what skills are available'. DO NOT TRIGGER when: user asks a task question like 'how do I do X' (route to the relevant domain skill instead), or is already using a specific skill. Use when this capability is needed.
metadata:
  author: baltsat
---

# Find Skills

Discover and install skills from the open agent skills ecosystem.

## When to Use

- User says "find a skill for X" or "is there a skill for X"
- User asks "what skills are available" or "can you install a skill"
- User wants to extend agent capabilities with new skills

## What is the Skills CLI?

The Skills CLI (`npx skills`) is the package manager for the open agent skills ecosystem.

**Key commands:**

- `npx skills find [query]` - Search for skills
- `npx skills add <package>` - Install a skill
- `npx skills check` - Check for updates
- `npx skills update` - Update all skills

**Browse skills at:** https://skills.sh/

## How to Help Users

### Step 1: Search for Skills

```bash
npx skills find [query]
```

Examples:

- "find a skill for react performance" → `npx skills find react performance`
- "is there a skill for PR reviews?" → `npx skills find pr review`
- "install a changelog skill" → `npx skills find changelog`

### Step 2: Present Options

When you find relevant skills, present them with:

1. The skill name and what it does
2. The install command
3. A link to learn more

Example:

```
I found a skill that might help! The "vercel-react-best-practices" skill provides
React and Next.js performance optimization guidelines.

To install it:
npx skills add vercel-labs/agent-skills@vercel-react-best-practices

Learn more: https://skills.sh/vercel-labs/agent-skills/vercel-react-best-practices
```

### Step 3: Offer to Install

```bash
npx skills add <owner/repo@skill> -g -y
```

The `-g` flag installs globally, `-y` skips confirmation.

## Common Skill Categories

| Category        | Example Queries                      |
| --------------- | ------------------------------------ |
| Web Development | react, nextjs, typescript, css       |
| Testing         | testing, jest, playwright, e2e       |
| DevOps          | deploy, docker, kubernetes, ci-cd    |
| Documentation   | docs, readme, changelog              |
| Code Quality    | review, lint, refactor               |
| Design          | ui, ux, design-system, accessibility |

## When No Skills Found

If no relevant skills exist:

1. Acknowledge that no existing skill was found
2. Offer to help with the task directly
3. Suggest creating their own skill: `npx skills init my-skill`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/baltsat) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
