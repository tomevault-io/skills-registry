---
name: peon-ping-toggle
description: Toggle peon-ping sound notifications on/off. Use when user wants to mute, unmute, pause, or resume peon sounds during a Claude Code session. Use when this capability is needed.
metadata:
  author: hao6yu
---

# peon-ping-toggle

Toggle peon-ping sounds on or off.

Run the following command using the Bash tool:

```bash
bash ~/.claude/hooks/peon-ping/peon.sh --toggle
```

Report the output to the user. The command will print either:
- `peon-ping: sounds paused` — sounds are now muted
- `peon-ping: sounds resumed` — sounds are now active

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hao6yu) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
