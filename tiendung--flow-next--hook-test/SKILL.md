---
name: hook-test
description: Test skill to verify frontmatter hooks work. Use when asked to test skill hooks. Use when this capability is needed.
metadata:
  author: tiendung
---

# Hook Test Skill

This skill tests if skill-level hooks fire.

When invoked, run a simple bash command like `echo hello` to trigger the PreToolUse hook.

Then check if `/tmp/skill-hook-test.log` exists and contains "SKILL HOOK FIRED".

Report: did the skill hook fire?

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tiendung) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
