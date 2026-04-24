---
name: research-doc
description: | Use when this capability is needed.
metadata:
  author: different-ai
---

## Quick Usage (Already Configured)

### Create a research doc and push
1. Parse the topic from the user prompt after "hey research".
2. Ensure `research/` exists; create it if missing.
3. Create `research/YYYY-MM-DD-<topic-slug>.md` (lowercase, hyphenated).
   - If the file already exists for the same day/topic, append `-2`, `-3`, etc.
4. Draft content using the template below.
5. `git add research/YYYY-MM-DD-<topic-slug>.md`
6. `git commit -m "docs: add research on <topic>"`
7. `git push` (set upstream if missing).

## Research Template

```markdown
# <Topic>

## Summary

## Key Points

## Findings

## Sources
- [Title](URL) - 1 line note
```

## Common Gotchas

- Prefer primary sources and docs; include 3-6 reputable links.
- If web access is unavailable, note "Sources pending" in the Sources section.
- Do not include secrets or sensitive data in the research doc or commit.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/different-ai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
