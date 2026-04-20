---
name: run
description: Drive an AI agent through the current scene in Play mode — test navigation, combat, loot, traps, and report issues like unreachable areas, stuck states, or broken triggers. Use when this capability is needed.
metadata:
  author: newtro
---

Run an AI playtest session: $ARGUMENTS

## Process

1. **Analyze the scene** — get hierarchy, find player spawn, exits, enemies, loot, traps, triggers
2. **Enter Play mode** via MCP
3. **Execute test sequence**:
   - Navigate between key points (spawn → exits, spawn → loot, spawn → enemies)
   - Screenshot periodically for visual analysis
   - Monitor console for errors and warnings
   - Track time spent in each area
4. **Test categories**:
   - **Navigation**: Can the player reach all exits? Any stuck points?
   - **Combat**: Do enemies spawn? Can they be damaged? Do they die properly?
   - **Loot**: Do items drop? Can they be collected? Do they grant effects?
   - **Traps**: Do triggers fire? Do damage zones work? Can they be avoided?
   - **UI**: Do health bars update? Does inventory reflect changes?
5. **Exit Play mode**
6. **Generate report**:
   - Issues found (with screenshots)
   - Areas tested vs. untested
   - Performance observations
   - Suggested fixes

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/newtro) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
