---
name: claude-restart-resume
description: name: claude-restart-resume Use when this capability is needed.
metadata:
  author: manuelkugelmann
---
---
name: claude-restart-resume
description: Quick restart to reload configuration changes (skills, settings, hooks, MCP services). Use PROACTIVELY after modifying .claude/ files. Preserves conversation history.
---

Restarting Claude Code to reload configuration...

This will:
- Reload all skills from .claude/skills/
- Reload settings from .claude/settings.json
- Reload hooks configuration
- Preserve your conversation history

```bash
.claude/skills/claude-restart-resume/scripts/claude-restart.sh resume
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/manuelkugelmann) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
