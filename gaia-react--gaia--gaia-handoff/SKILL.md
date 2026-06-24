---
name: gaia-handoff
description: Generate a comprehensive GAIA session handoff document — accomplishments, decisions, current state, open questions — so context can be cleared or compacted without losing anything. Trigger on `/gaia-handoff` or natural-language asks like "write a handoff", "hand off this session", or "document where we are before I clear context". Use when this capability is needed.
metadata:
  author: gaia-react
---

Run the GAIA **handoff** workflow with these arguments: `$ARGUMENTS`

Read `.claude/skills/gaia/references/handoff.md` from the project root and follow it exactly. Treat the arguments above as optional inline notes (decisions, gaps, open questions); if empty, derive everything from the conversation transcript and git state per the reference.

---
> Source: [gaia-react/gaia](https://github.com/gaia-react/gaia) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-04 -->
