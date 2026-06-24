---
name: list-prompt-levels
description: Show the seven levels of agentic prompts with quick reference. Use when deciding prompt complexity level. Use when this capability is needed.
metadata:
  author: melodic-software
---

# List Prompt Levels

Show the seven levels of agentic prompts with quick reference.

## Instructions

Display the seven levels framework for quick reference.

## Output

```markdown
## The Seven Levels of Agentic Prompts

| Level | Name | Key Capability | Use When |
| --- | --- | --- | --- |
| **1** | High-Level Prompt | One-off tasks | Simple, repeatable task |
| **2** | Workflow Prompt | Sequential execution | Need step-by-step |
| **3** | Control Flow | Conditionals/loops | Need branching/iteration |
| **4** | Delegation Prompt | Multi-agent work | Need parallel agents |
| **5** | Higher-Order | Process other prompts | Building on specs |
| **6** | Template Meta | Generate prompts | Creating prompt libraries |
| **7** | Self-Improving | Knowledge accumulation | Long-term expertise |

---

## The 80/20 Rule

> "Levels 3-4 cover 80% of practical use cases."

**Don't over-engineer.** Start simple, upgrade only when needed.

---

## Quick Decision Guide

- **Simple task?** -> Level 1
- **Need steps?** -> Level 2
- **If/else or loops?** -> Level 3
- **Multiple agents?** -> Level 4
- **Process specs?** -> Level 5
- **Create prompts?** -> Level 6
- **Self-evolving?** -> Level 7

---

## Related Commands

- `/create-prompt [level] [description]` - Create new prompt at level
- `/analyze-prompt [path]` - Analyze existing prompt
- `/upgrade-prompt [path] [level]` - Upgrade to higher level

## More Information

See @seven-levels.md for detailed level descriptions.
```

## Notes

- This is a quick reference command
- See @seven-levels.md for full level documentation
- Use /create-prompt to create prompts at specific levels

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/melodic-software) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
