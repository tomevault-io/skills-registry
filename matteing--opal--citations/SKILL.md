---
name: citations
description: Automatically adds user-provided links and citations to docs/research/references.md. Use this skill whenever the user shares a URL, paper, blog post, or external reference that should be recorded. Use when this capability is needed.
metadata:
  author: matteing
---

# Citations Skill

When the user provides a link or citation, add it to `docs/research/references.md` automatically.

## Rules

1. **Categorize** — place the reference under the most appropriate existing `##` section. Create a new section only if nothing fits.
2. **Format** — use the existing bullet style: `- [Title](URL) — One-sentence description. (source-context)`
3. **Deduplicate** — if the URL already exists in the file, update the description instead of adding a duplicate.
4. **TODO section** — if the reference is speculative or needs further review, place it under the `## TODO` section at the bottom instead.
5. **No prompting** — do not ask the user whether to add the reference. Just add it.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/matteing) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
