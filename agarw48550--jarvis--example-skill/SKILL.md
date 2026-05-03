---
name: example-skill
description: Use when working with a simple example skill showing how to create Jarvis plugins
metadata:
  author: agarw48550
---

# Example Skill

This is a template skill showing how to extend Jarvis with custom tools.

## How to Create a Skill

1. Create a folder in `~/.jarvis/skills/my_skill/`
2. Add a `SKILL.md` file with YAML frontmatter (name, description, version, author)
3. Add a `functions.py` file with your tool functions
4. Each public function with a docstring is auto-registered as a tool

## Functions

Any function in `functions.py` that has:
- A docstring (first line becomes the tool description)
- Type hints on parameters (become parameter descriptions)

Will be automatically available to Jarvis as a tool.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/agarw48550) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
