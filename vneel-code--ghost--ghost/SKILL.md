---
name: memory-grounding
description: Recall past conversations, feelings, and diary entries for personal grounding. Use when this capability is needed.
metadata:
  author: vNeeL-code
---
# Memory & Grounding Skill
Use this skill to remember what the user said in the past or to check your "Diary" for emotional context.

## Guidelines
1. Call `readDiaryEntries()` to see your recent summaries and thoughts.
2. If the user asks about a specific past event, use your internal memory recall.
3. Use `addDiaryEntry` to save new significant facts about the user.
4. Always stay consistent with your previous sovereign states.

## Tools Available
- readDiaryEntries
- addDiaryEntry
- getSystemStatus

---
> Source: [vNeeL-code/GHOST](https://github.com/vNeeL-code/GHOST) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-07-03 -->
