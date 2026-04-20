---
name: markdown-skill-reviewer
description: Reviews Agent Skill files (SKILL.md) for style and structural consistency using markdownlint-cli2. Use when this capability is needed.
metadata:
  author: pluto-atom-4
---

# Skill Instructions

Use this skill to ensure all `SKILL.md` files follow project standards
and are free of formatting errors.

## Workflow

1. **Identify Target:** Locate the `SKILL.md` file or directory to be reviewed.
2. **Configuration Check:** Ensure a `.markdownlint-cli2.yaml` or `.jsonc`
   exists in the root; if not, use the default project rules.
3. **Execution:** Run the following command:

   ```bash
   npx markdownlint-cli2 "**/SKILL.md"

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/pluto-atom-4) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
