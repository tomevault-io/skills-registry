---
name: ynh-validate
description: Validate Claude Code artifacts (skills, agents) in the .claude/ directory. Checks frontmatter, naming conventions, and referenced paths. Use when this capability is needed.
metadata:
  author: eyelock
---

# Validate Artifacts

Run the validation script to check all `.claude/` artifacts:

```bash
bash .claude/skills/ynh-validate/scripts/validate.sh
```

The script checks:
- Skills have valid YAML frontmatter (`name`, `description`)
- Agents have valid YAML frontmatter (`name`, `description`, `tools`)
- `name` fields match their filename or directory name
- Frontmatter delimiters (`---`) are present

If any checks fail, fix the reported issues and re-run until clean.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/eyelock) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
