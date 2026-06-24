---
name: auto-skill-creation
description: When a repeated workflow should be codified as a reusable skill → auto-create skill files, optionally using Codex/Claude. Use when this capability is needed.
metadata:
  author: cklxx
---

# auto-skill-creation

Automatically create new skills from repeated task patterns by dispatching to external agents (Codex/Claude), collecting results, and generating compliant skill directory structures. All task dispatch, status monitoring, and file generation logic are handled by run.py.

## 调用

```bash
python3 skills/auto-skill-creation/run.py create --skill_name my-new-skill --description 'What the skill does' --agent_type codex
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/cklxx) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
