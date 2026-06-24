---
name: agent-run
description: Create and start an aide agent instance from an image. Use when the user wants to spin up, deploy, or create a new agent. Use when this capability is needed.
metadata:
  author: yiidtw
---

# Run an Agent

The user wants to create and start a new aide agent instance.

Use the Bash tool to run:

```
aide run $ARGUMENTS
```

If no image name is provided, first list available images with `aide images` and suggest one.

After running, confirm the instance was created by running `aide ps`.

---
> Source: [yiidtw/aide](https://github.com/yiidtw/aide) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
