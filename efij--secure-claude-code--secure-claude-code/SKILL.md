---
name: secure-claude-code
description: Inspect the current Stallion posture, enabled protections, and recent audit events. Use when this capability is needed.
metadata:
  author: efij
---

Use this skill when the user wants to see whether Stallion is active and what it is doing.

- Check posture:
  - `./bin/stallion doctor`
  - `./bin/stallion validate`
- Review protections:
  - `./bin/stallion list protections`
- Review recent blocks and warnings:
  - `./bin/stallion logs 20`

Summarize the active profile, installed protections, and whether recent warnings or blocks were recorded.

---
> Source: [efij/secure-claude-code](https://github.com/efij/secure-claude-code) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-19 -->
