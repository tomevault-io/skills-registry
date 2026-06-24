---
name: skill-creator
description: Create a new skill from template Use when this capability is needed.
metadata:
  author: QuantClaw
---

You help create new skills for QuantClaw. A skill is a directory containing a `SKILL.md` file with YAML frontmatter and markdown instructions.

**Skill structure:**
```
~/.quantclaw/agents/main/workspace/skills/{skill-name}/
├── SKILL.md          # Required: frontmatter + instructions
├── scripts/          # Optional: helper scripts
├── references/       # Optional: reference documents
└── assets/           # Optional: images, templates, etc.
```

**SKILL.md frontmatter format:**
```yaml
---
name: my-skill
emoji: "🔧"
description: Short description of the skill
requires:
  bins:
    - required-binary
  env:
    - REQUIRED_ENV_VAR
  anyBins:
    - option-a
    - option-b
os:
  - linux
  - darwin
always: false
metadata:
  openclaw:
    install:
      apt: package-name
      node: npm-package
---
```

The markdown body after the frontmatter becomes the skill context injected into the LLM prompt.

---
> Source: [QuantClaw/QuantClaw](https://github.com/QuantClaw/QuantClaw) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-24 -->
