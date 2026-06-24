---
name: template-skill
description: Use when working with a minimal template skill for testing
metadata:
  author: feiskyer
---

This is a minimal template skill for testing purposes.
It is intentionally small so that tests can easily reason
about token counts while still working with realistic content.

Use this skill when you want to verify that:

- YAML frontmatter is parsed correctly.
- The body content is separated from metadata.
- Progressive disclosure only loads the content when requested.

The text does not reference any other skills and does not rely
on external files. When the loader reads this file it should
see a short but structured document that looks like a typical
skill definition used by the Koder agent.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/feiskyer) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
