---
name: cc-solver
description: Targeted Claude Code problem solver for specific issues and implementation questions. Use when the user has a specific problem to solve, needs help with a particular feature, is debugging an issue, or wants to implement something specific with Claude Code. Triggers on phrases like "how do I", "I'm trying to", "not working", "error with", "configure", "implement", "create a hook", "set up MCP", "fix this", "why isn't". Use when this capability is needed.
metadata:
  author: mikkelkrogsholm
---

# Claude Code Problem Solver Skill

This skill triggers targeted problem-solving when Claude Code issues are detected.

## When This Skill Activates

This skill should activate when the user has specific problems or questions about Claude Code, such as:
- "How do I configure..."
- "Why isn't my hook working..."
- "I'm trying to set up MCP..."
- "Error when creating a skill..."
- "My plugin isn't loading..."

## Action

When this skill is triggered, use the Task tool to spawn the **cc-solver** agent:

```
Task(
  subagent_type="cc-solver",
  prompt="Solve this Claude Code problem: [user's problem]",
  description="Solve Claude Code problem"
)
```

The cc-solver agent will:
1. Check the changelog for recent changes or fixes
2. Fetch only the 2-4 most relevant documentation pages
3. Provide focused, copy-paste ready solutions

## Output

Return the agent's focused solution to the user, including:
- Direct answer to their specific question
- Copy-paste ready code or configuration
- Explanation of why the solution works
- How to verify it's working

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mikkelkrogsholm) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
