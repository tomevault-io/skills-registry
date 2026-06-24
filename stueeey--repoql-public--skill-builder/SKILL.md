---
name: skill-builder
description: This skill guides intentional skill design. Use when creating, improving, or reviewing Claude Code skills. Requires a zone assessment to clarify what kind of skill is being built before writing content. Use when this capability is needed.
metadata:
  author: stueeey
---

# Skill Builder

A skill encodes what cannot be derived from first principles. Before writing, understand what you're encoding.

## Load

Skill files are irreducible — they cannot be summarized and still communicate what they need to. Browse what's available, then read each file you need with `=> content` to get full text — never structure.

```
read("help:///skills/skill-builder/** => tree: headlines", 5000)
```

Then read each file you need:

```
read("help:///skills/skill-builder/SKILL.md => content", 10000)
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/stueeey) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
