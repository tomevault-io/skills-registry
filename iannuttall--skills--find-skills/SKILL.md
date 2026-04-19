---
name: find-skills
description: Find and install agent skills with `npx playbooks find skill` and `npx playbooks add skill`. Use whenever a skill needs to be discovered or installed. Use when this capability is needed.
metadata:
  author: iannuttall
---

# find-skills

- Search: `npx playbooks find skill "<query>"`
- Search is lexical by default; use `--semantic` for natural language search
- Output is ordered by best match and prints ready-to-run install commands
- If a repo/source is known, you can list skills: `npx playbooks add skill <source> --list`
- List agent IDs: `npx playbooks list agents`
- Ask which agent(s) to install to and whether global (`-g`) or project
- Install a specific skill: `npx playbooks add skill <source> --skill <skill-name> -a <agent> [-g] [-y]`
- Install all skills from a source only if user requests: `npx playbooks add skill <source> --all`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/iannuttall) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
