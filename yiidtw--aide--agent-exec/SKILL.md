---
name: agent-exec
description: Execute a skill on a running aide agent instance. Use when the user wants to run a command or skill on an existing agent. Use when this capability is needed.
metadata:
  author: yiidtw
---

# Execute Agent Skill

The user wants to execute a skill on an aide agent instance.

Use the Bash tool to run:

```
aide exec $ARGUMENTS
```

If the user didn't specify which instance or skill, first run `aide ps` to list instances, then run `aide exec <instance> --help` to show available skills for that instance.

---
> Source: [yiidtw/aide](https://github.com/yiidtw/aide) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
