---
name: amp-handoff
description: Hands off the current Claude Code session to Amp. Use when asked to hand off to Amp, continue in Amp, or transfer work to Amp. Use when this capability is needed.
metadata:
  author: dropalltables
---

# Amp Handoff

Generate a handoff summary for continuing work in Amp.

## Goal

$ARGUMENTS

## Instructions

**If a goal is specified above:**
- Tailor your summary to focus ONLY on information relevant to that goal
- Filter out unrelated work from the summary
- Be ruthlessly focused

**If no goal is specified:**
- Summarize all work in this session

**Generate a concise handoff summary in this format:**

```
**Accomplished:**
- [bullet points - if goal provided, only items related to goal]

**Key files:**
- [file paths - if goal provided, only files related to goal]

**Current state:**
- [status, any blockers]

**Next steps:**
- [remaining work - if goal provided, only steps related to goal]
```

**Note:** Keep it brief - Amp can use `tb__read_cc_thread` for full context

**CRITICAL:** If there is text in the "## Goal" section above, output it at the end wrapped in tags like: <goal>the goal text</goal>

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/dropalltables) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
