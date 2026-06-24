---
name: open-gui
description: | Use when this capability is needed.
metadata:
  author: Walrus-Computing
---

# /open-gui — Launch the piper-draw GUI

Run the helper script and print its final line. One bash call, no follow-ups.

```bash
.claude/skills/open-gui/start.sh
```

The script handles everything: derives per-workspace ports, short-circuits if
the server is already up, sources nvm for Node 22, backgrounds `make dev`,
waits for Vite, and opens the browser. On success it prints one line like:

    started in 4s  frontend=http://localhost:5245/  backend=http://127.0.0.1:8072  log=/tmp/open-gui-manama.log

On failure it prints a tail of the log and exits non-zero — relay that to
the user unchanged.

---
> Source: [Walrus-Computing/piper-draw](https://github.com/Walrus-Computing/piper-draw) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-22 -->
