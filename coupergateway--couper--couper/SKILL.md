---
name: init-context
description: Load full project context by reading all documentation linked from CLAUDE.md Use when this capability is needed.
metadata:
  author: coupergateway
---

# Init Context

1. Parse `CLAUDE.md` and find all markdown links to local documentation files (paths like `docs/*.md` or relative `.md` links)
2. Read each linked documentation file fully
3. Confirm context is loaded with a brief summary of key concepts

---
> Source: [coupergateway/couper](https://github.com/coupergateway/couper) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-23 -->
