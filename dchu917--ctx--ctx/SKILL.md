---
name: ctx-resume
description: Resume a ContextFun workstream and print a markdown pack. Use when this capability is needed.
metadata:
  author: dchu917
---

Usage
- In chat:
  - `/ctx resume <workstream>` — resumes that workstream
  - If `CTX_AGENT_WORKSTREAM` is set, the name can be omitted

What it runs
- `bash ./scripts/ctx_resume.sh`
- Falls back to `ctx resume` or `python3 scripts/ctx_cmd.py resume`

Notes
- The workstream must already exist. Use `/ctx start <workstream>` to create a new one.
- Uses local transcript storage (default `~/.claude/projects`, `~/.codex/sessions`).
- Initialize ContextFun with `scripts/quickstart.sh`.

---
> Source: [dchu917/ctx](https://github.com/dchu917/ctx) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-24 -->
