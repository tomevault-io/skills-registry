---
name: agent-list
description: List running aide agent instances and their status. Use when the user asks about running agents, agent status, or wants to see what agents are active. Use when this capability is needed.
metadata:
  author: yiidtw
---

# List Agent Instances

The user wants to see running aide agent instances.

Use the Bash tool to run:

```
aide ps
```

Present the output in a clear format. If no instances are running, suggest creating one with `aide run <image>`.

---
> Source: [yiidtw/aide](https://github.com/yiidtw/aide) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
