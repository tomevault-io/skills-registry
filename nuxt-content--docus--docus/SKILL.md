---
name: docus-playground
description: Sample skill for testing the Docus agent skills discovery feature. Use to verify that /.well-known/skills/ routes work correctly. Use when this capability is needed.
metadata:
  author: nuxt-content
---

# Docus Playground Skill

This is a sample skill used to test the agent skills discovery feature in the Docus playground.

## Verify Discovery

Check these endpoints:

- `GET /.well-known/skills/index.json` -- should list this skill
- `GET /.well-known/skills/docus-playground/SKILL.md` -- should return this file
- `GET /.well-known/skills/docus-playground/references/example.md` -- should return the reference file

For more details, see [references/example.md](references/example.md).

---
> Source: [nuxt-content/docus](https://github.com/nuxt-content/docus) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-27 -->
