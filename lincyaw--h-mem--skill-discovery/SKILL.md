---
name: skill-discovery
description: Use before starting any task to find relevant skills Use when this capability is needed.
metadata:
  author: lincyaw
---

# Skill Discovery

## Iron Law

**NO TASK EXECUTION WITHOUT SKILL SEARCH FIRST.**

Before starting any task, you MUST search for relevant skills. Even a 1% chance that a skill might apply means you should search.

## When to Search

```
Decision Tree:
├── New task received?
│   └── YES → Search skills immediately
├── Encountering unfamiliar problem?
│   └── YES → Search skills
├── About to use a common pattern?
│   └── YES → Search for existing skill
├── Task similar to past experience?
│   └── YES → Search for induced skills
└── All else
    └── Search anyway (it costs nothing)
```

## How to Search

1. **Use SkillSearchTool** with a descriptive query:
   - Include the problem domain: "memory leak debugging"
   - Include the action type: "extracting entities from text"
   - Include the context: "Neo4j graph optimization"

2. **Evaluate results** using these criteria:
   - Q-value > 0.7: High confidence, use directly
   - Q-value 0.4-0.7: Moderate confidence, use with judgment
   - Q-value < 0.4: Low confidence, verify before using
   - No results: Proceed without skill, but log the gap

3. **Load matching skills** with SkillLoadTool:
   - Load the top 1-2 matching skills
   - Read the full content before proceeding
   - Follow the skill's instructions precisely

## Anti-patterns

- Skipping search because "this is a simple task"
- Searching with vague queries ("help")
- Ignoring search results because they seem familiar
- Loading skills but not reading them
- Using outdated mental model instead of checking for updates

## After the Task

- If a skill was helpful → provide "success" feedback
- If a skill was NOT helpful → provide "failure" feedback
- If no skill existed but should have → note this for skill creation

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/lincyaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
