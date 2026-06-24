---
name: mp3-diagnose
description: Diagnose MP3 comment field conflicts between MediaMonkey and Serato. Use when the user wants to inspect ID3 comment tags on an MP3 file to understand why apps show different comment values. Use when this capability is needed.
metadata:
  author: gwherrett
---

Diagnose comment field conflicts in an MP3 file by running the diagnosis script.

## Execution

Run the following command with the user-provided file path:

```
python /workspaces/mako-sync/python/diagnose_comments.py $ARGUMENTS
```

## Instructions

- Present the diagnosis results clearly, grouping COMM frames and TXXX frames separately
- Explain what each COMM frame means: description, language, and which app typically uses it
- Highlight any conflicts — e.g., multiple COMM frames that different apps (MediaMonkey, Serato) would read differently
- If problematic fields are found (ID3v1 Comment, empty-description XXX frames, iTunes normalization data), suggest using `/mp3-fix-comments` to resolve them
- If the file looks clean, confirm that no conflicts exist

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/gwherrett) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
