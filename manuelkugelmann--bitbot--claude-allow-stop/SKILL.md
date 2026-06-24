---
name: claude-allow-stop
description: name: claude-allow-stop Use when this capability is needed.
metadata:
  author: manuelkugelmann
---
---
name: claude-allow-stop
description: Return to normal stop behavior. Use PROACTIVELY after completing all tasks in continuous work mode, when work is committed and tests pass, or when reaching natural stopping point. User can invoke with /claude-allow-stop.
allowed-tools: Bash, Read
---

# Disable Stop Hook Automation

Allow normal completion and wait for user input.

```bash
.claude/skills/claude-allow-stop/scripts/allow-stop.sh
```

Or user invokes: `/claude-allow-stop`

Re-enable with `/claude-do-not-stop [reason]`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/manuelkugelmann) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
