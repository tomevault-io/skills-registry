---
name: browser
description: When testing web UI, load this skill to take screenshots and interact with elements. Use when this capability is needed.
metadata:
  author: djgrant
---

## ::workflow::

DO
  1. open <url> to start session
  2. snapshot to see current state and get @refs
  3. interact with elements (click, type, etc.) using @refs or selectors
  4. screenshot to verify result
END


## Commands

```bash
agent-browser <command>
```

Common commands: `open <url>`, `snapshot`, `click <selector|@ref>`, `type <selector|@ref> <text>`, `screenshot`.
Use `agent-browser --help` for full command list.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/djgrant) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
