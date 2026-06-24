---
name: context7-skill
description: Fetch up-to-date documentation for any library using the Context7 API. Use when the user needs accurate, current docs for a framework, library, or SDK (e.g., React, Next.js, Langchain). Two-step process: first search for the library, then fetch context. Use when this capability is needed.
metadata:
  author: jeric-n
---

# Context7 Documentation Fetcher

Retrieves accurate, up-to-date documentation from Context7 for any library or framework.

## When to Use

- User asks about a specific library's API or usage
- You need current documentation (your training data may be outdated)
- User mentions a framework like React, Next.js, Vue, Langchain, etc.

## Prerequisites

Ensure `CONTEXT7_API_KEY` environment variable is set.

## Workflow

### Step 1: Search for the Library

Find the library ID by searching. Replace `<skill-dir>` with the path to where this skill is installed:
- Project-wide: `.gemini/skills/context7-skill` or `.opencode/skills/context7-skill`
- Global: `~/.gemini/skills/context7-skill` or `~/.config/opencode/skills/context7-skill`

```bash
node <skill-dir>/scripts/index.js search "<library-name>" "<your-question>"
```

Example (global install):
```bash
node ~/.gemini/skills/context7-skill/scripts/index.js search "react" "hooks"
```

This returns matching libraries with their IDs (e.g., `/facebook/react`).

### Step 2: Fetch Documentation Context

Use the library ID to get relevant documentation:

```bash
node <skill-dir>/scripts/index.js context "<library-id>" "<your-question>"
```

Example (global install):
```bash
node ~/.gemini/skills/context7-skill/scripts/index.js context "/facebook/react" "how to use useState hook"
```

## Tips

- Be specific with your query to get more relevant results
- Use the exact library ID from the search results
- If results seem outdated, try a more specific version like `/vercel/next.js/v15.1.8`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jeric-n) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
