---
name: agent-zero-planning
description: Complex task planning and breakdown using Agent Zero Use when this capability is needed.
metadata:
  author: jason-easyazz
---
# Agent Zero Planning

## When to Use
User wants to plan a complex task, project, or event. Agent Zero will decompose the task into actionable steps.

## API Endpoints

### Submit Planning Task
POST http://agent-zero-bridge:8101/tools/task
```json
{"task": "plan description", "context": "relevant context"}
```

## Behavior
1. Understand the scope of what needs planning
2. Delegate to Agent Zero for detailed breakdown
3. Present as numbered steps with estimated effort
4. Offer to save the plan to Zoe's task list

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jason-easyazz) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
