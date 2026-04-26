---
name: skill-rules-reference
description: Skill rules and constraints reference. Keywords: skill, rules, reference. Use when this capability is needed.
metadata:
  author: willyu1007
---
# Skill Rules Reference (Legacy)

This document is kept for historical context.

**Current model**:
- Skill discovery is primarily driven by `description` in SSOT `/.system/skills/ssot/**/<skill-name>/SKILL.md`.
- Provider wrappers are generated under `/.codex/skills/**` and `/.claude/skills/**` by `sync_skills.py`.
- Optional prompt-time hints (if used) live under `/.system/skills/config/`.

The previous `/.system/skills/skill-rules.json` model is deprecated and no longer used by the template tooling.

---

## Legacy JSON shape (v1.x)

```json
{
  "version": "1.0",
  "description": "Skill selection triggers for the AI-first template.",
  "skills": {
    "backend-dev-guidelines": {
      "entrypoint": "/.system/skills/ssot/backend/backend-dev-guidelines/backend-dev-guidelines/skill/_skill_stub.md",
      "type": "domain",
      "enforcement": "suggest",
      "priority": "high",
      "description": "Backend development patterns for Node.js/Express/TypeScript",
      "promptTriggers": {
        "keywords": ["controller", "service", "repository", "middleware", "route"],
        "intentPatterns": ["(create|add).*?(route|controller|service)"]
      },
      "fileTriggers": {
        "pathPatterns": ["modules/**/src/**/*.ts", "scripts/**/*.ts"],
        "pathExclusions": ["**/*.test.ts", "**/*.spec.ts"],
        "contentPatterns": ["router\\\\.", "app\\\\.(get|post)"]
      }
    }
  }
}
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/willyu1007) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
