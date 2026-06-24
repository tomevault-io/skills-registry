---
name: list-components
description: List project components Use when this capability is needed.
metadata:
  author: bradtraversy
---

## Task

List all React component files (.tsx, .ts, .jsx, .js) in the components folder.

If a [subdirectory] is provided via $ARGUMENTS, only list files in that subdirectory.

## Output Format

- Numbered list of files with relative paths
- Brief one-line description of each (infer from filename)
- Summary count at the end

If no files found, say "No components found."

---
> Source: [bradtraversy/devstash](https://github.com/bradtraversy/devstash) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-22 -->
