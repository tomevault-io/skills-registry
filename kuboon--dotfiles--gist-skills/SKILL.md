---
name: gist-skills
description: How to save & load skills from my gist Use when this capability is needed.
metadata:
  author: kuboon
---

# metadata file
Each skill directory should have `.<skill-name>.SKILL.md` that will contain matadata.
```
<skill-description>

- updated_on: YYYY-MM-DD
- [gist](<gist-url>)
```
Because gist list page shows filename, `SKILL.md` is not helpful.

# List skills
`gh gist list --filter "SKILL.md <other queries>"`

# Load skill
- get gist-id by "List skills"
- get contents by `gh gist view <gist-id>`
- save them to `<workdir-root>/.agents/skills/<skill-name>/*`

# Save or update
- metadata file is not exist or no gist-url saved:
  - `gh gist create .<skill-name>.SKILL.md -p -d "<skill-name>/SKILL.md <tag> <tag> <tag>"`
- update `updated_on` field in metadata
- `gh gist edit <gist-id> "<update-filename>"`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kuboon) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
