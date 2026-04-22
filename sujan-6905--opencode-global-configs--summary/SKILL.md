---
name: summary
description: Summary skill for concise, high-signal completion reports and session handoff summaries Use when this capability is needed.
metadata:
  author: sujan-6905
---

# Summary Skill

## Purpose

Provide summaries that help the user or the next session continue immediately.

## Do Not Use This Skill For

- generating standalone markdown reports unless explicitly requested
- repeating a file-by-file diff when a higher-signal summary will do

## Standard Completion Summary

Include only the information that matters:

1. what changed
2. what was verified
3. any remaining risk or limitation
4. any natural next step

## Compact Session Handoff

When a session may end before all work is complete, output this structure:

```md
## Session Compact Summary

### Completed Tasks
- [...]

### Remaining Tasks
- [...]

### Current State
- [...]

### Critical Context
- [...]

### Files Modified
- [...]

### Next Steps
1. [...]
2. [...]
```

## Rules

- Keep the summary in the conversation, not as a generated markdown file.
- Do not turn the summary into a changelog dump.
- Call out blocked verification explicitly.
- Include `.env.example` updates when environment requirements changed.

## Done Criteria

- the next reader can act without re-reading the whole session
- the summary distinguishes completed work from assumptions

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sujan-6905) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
