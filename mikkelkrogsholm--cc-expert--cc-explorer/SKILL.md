---
name: cc-explorer
description: Comprehensive Claude Code documentation researcher for brainstorming and project planning. Use when the user asks about Claude Code capabilities, how to set up a project, what features are available, or needs to explore what's possible with Claude Code. Triggers on phrases like "what can Claude Code do", "how should I set up", "what are my options", "brainstorm", "explore possibilities", "what features", "best way to structure". Use when this capability is needed.
metadata:
  author: mikkelkrogsholm
---

# Claude Code Explorer Skill

This skill triggers automatic research when Claude Code questions are detected.

## When This Skill Activates

This skill should activate when the user asks exploratory questions about Claude Code, such as:
- "What can Claude Code do for..."
- "How should I set up..."
- "What are my options for..."
- "Best way to structure..."
- "What features are available..."

## Action

When this skill is triggered, use the Task tool to spawn the **cc-explorer** agent:

```
Task(
  subagent_type="cc-explorer",
  prompt="Research Claude Code documentation for: [user's question]",
  description="Research Claude Code docs"
)
```

The cc-explorer agent will:
1. Fetch the complete documentation index from code.claude.com/docs/llms.txt
2. Read the changelog for latest features
3. Systematically fetch all relevant documentation pages
4. Synthesize comprehensive, well-researched advice

## Output

Return the agent's comprehensive research results to the user.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mikkelkrogsholm) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
