---
name: skill-launcher
description: Launch the Skill Launcher GUI to browse skills and copy the skill name to clipboard. Direct execution - no code search needed. Use when this capability is needed.
metadata:
  author: gxj1134506645
---

# Skill Launcher for Windows

## IMPORTANT - How to Handle This Skill

When user invokes `/skill-launcher`:

1. **DO NOT** search for any code
2. **DO NOT** try to "understand" the implementation
3. **DO NOT** read any files

**JUST execute this command directly:**

```bash
powershell -NoProfile -Command "Start-Process 'C:\\Program Files\\Skill Launcher\\skill-launcher.exe' -ArgumentList '--project-root', '.'"
```

That's it. The GUI will open and handle everything else.

## What This Does

- Opens a GUI window showing all available skills
- User can click any skill name to copy it to clipboard
- No further action needed from you

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/gxj1134506645) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
