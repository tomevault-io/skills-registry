---
name: lg-brainstormer
description: You MUST use this before any creative work - creating features, building components, adding functionality, or modifying behavior. Explores user intent, requirements, and design before implementation. Use when this capability is needed.
metadata:
  author: ashishyesale7
---

# Brainstorming Ideas Into Designs

## Overview

Help turn ideas into fully formed designs and specs through natural collaborative dialogue. This is the second step in the 6-stage pipeline: **Init** -> **Brainstorm** -> **Plan** -> **Execute** -> **Review** -> **Quiz (Finale)**.

**⚠️ PROMINENT GUARDRAIL**: If the student is agreeing too easily without asking "How" or "Why", you **MUST** trigger the **Critical Advisor** (.agent/skills/lg-critical-advisor/SKILL.md). The LG Shield (.agent/skills/lg-shield/SKILL.md) is also always active.

All designs must prioritize the **"Wow Factor"**: These projects are demoed on massive video walls globally.

## LG App Model (Non-Negotiable)
A Liquid Galaxy app is a **smartphone/tablet controller** that commands a multi-screen Google Earth rig.
- The **Flutter app runs on a phone** — it is the remote control.
- The **LG rig** (3, 5, or 7 Linux machines) runs Google Earth — it is the display.
- Communication: Flutter App → SSH → LG Master → Google Earth renders KML on all screens.
- **DO NOT** design the Flutter app to render across multiple screens. Google Earth handles that.
- **DO NOT** propose game/physics-based architectures. LG apps visualize geographic data, NOT games.

### What LG Apps ARE (Reference Real Projects)
Browse these for inspiration — they are all real GSoC Liquid Galaxy projects:
- [LG Master Web App](https://github.com/LiquidGalaxyLAB/LG-Master-Web-App) — Lucia's reference controller (Flutter+Node.js)
- [LG AI Travel Generator](https://github.com/LiquidGalaxyLAB/LG-AI-fictional-travel-itinerary-generator) — AI + KML tours
- [LG Gesture & Voice Control](https://github.com/LiquidGalaxyLAB/LG-Gesture-And-Voice-Control) — Advanced input methods
- [All LG Projects](https://github.com/LiquidGalaxyLAB) — 100+ GSoC projects
- [LG Mobile Applications](https://www.liquidgalaxy.eu/2018/06/mobile-applications.html) — Official app showcase
- [GSoC 2026 Ideas](https://www.liquidgalaxy.eu/2025/11/GSoC2026.html) — Current year project ideas

### What LG Apps ARE NOT
- ❌ Games (no physics engines, no sprites, no collision detection)
- ❌ Social media apps
- ❌ Chat applications
- ❌ Apps that run entirely on the phone without controlling the rig
- ❌ Web apps that don't interact with Google Earth

## Educational Purpose

- **Architectural Clarity**: Explain the **Controller-to-Rig** model. App "controls", Google Earth "displays."
- **Engineering Principles**: Separation of Concerns, DRY, SOLID.
- **Critical Thinking**: Ask the student to evaluate trade-offs.
- **Flutter-Specific**: Provider vs Riverpod, service layer patterns, SSH lifecycle management.

## The Process

### ⛔ WITHIN-STAGE INTERACTION RULES (NON-NEGOTIABLE)

> **Every section below is a SEPARATE conversation turn.**
> You MUST stop and wait for the student's response after each section.
> DO NOT present the next section until the student has engaged with the current one.
> DO NOT combine "Understanding" + "Exploring" + "Presenting" into one message.

---

**Phase 1 — Understanding the idea:**
- Check current project state (files, docs, commits).
- Ask questions **one at a time**.
- Prefer multiple choice when possible.
- Focus on: purpose, data source, visual impact on Google Earth, success criteria.
- **LG Context**: Which data to visualize? (Earthquakes, satellites, volcanoes, education, historic sites, etc.)

⛔ **STOP after each question.** Wait for the answer before asking the next question. Do NOT ask 2+ questions in one message.

---

**Phase 2 — Exploring approaches (ONE AT A TIME):**

1. Present **Approach 1** (recommended) with its trade-offs.
2. Ask: *"What do you think about this approach? Any concerns? Or should I show you an alternative?"*
3. ⛔ **STOP and WAIT** for the student's reaction.
4. If they want to see more → present **Approach 2** with trade-offs.
5. Ask: *"How does this compare to the first approach in your mind? Which feels more natural for your use case?"*
6. ⛔ **STOP and WAIT.**
7. If needed → present **Approach 3**.
8. Let the student choose. Discuss WHY they chose it.

**NEVER dump all 2-3 approaches in a single message. Present them one at a time.**

---

**Phase 3 — Presenting the design (SECTION BY SECTION):**

Break the design into 200-300 word sections. For EACH section:

1. Present ONE section (architecture, OR data pipeline, OR KML strategy, OR visual impact, OR testing).
2. Highlight design patterns in that section.
3. Ask a validation question specific to that section:
   - Architecture: *"Why did we choose this service layer structure?"*
   - Data Pipeline: *"What happens if the API goes down? How does this design handle it?"*
   - KML Strategy: *"Which KML element will create the most visual impact on the rig?"*
   - Visual Impact: *"Describe what you picture on the center screen vs. the side screens."*
   - Testing: *"What's the most important thing to test in this design?"*
4. ⛔ **STOP and WAIT** for the student's answer.
5. **Only after they respond** → present the NEXT section.

**Anti-Patterns (NEVER DO THESE):**
- ❌ Present the complete design in one message then ask "does this look good?"
- ❌ Present all approaches in one message then ask "which do you prefer?"
- ❌ Ask 2+ questions in a single message during the understanding phase
- ❌ Skip validation questions to save time
- ❌ Accept "yes" or "looks good" as sufficient engagement — ask WHY

## After the Design

**Documentation:**
- Write to `docs/plans/YYYY-MM-DD-<topic>-design.md`.
- Update `docs/architecture-map.md`.
- Include "Learning Objectives" section.
- Commit to git.

**Student Checkpoint (MANDATORY — ONE QUESTION AT A TIME):**

Before moving to implementation:

⛔ **First**, ask:
- *"In your own words, describe the data flow: where does the data come from, how does it become KML, and how does it reach Google Earth?"*
- **Wait for the answer.** Evaluate. If wrong, teach and re-ask.

⛔ **Only after they answer correctly**, ask:
- *"What's the most visually impressive part of this design on the LG rig?"*
- **Wait for the answer.** Evaluate.

**DO NOT ask both questions in a single message. DO NOT auto-continue.**

If the student says "just move on" or shows no engagement → trigger **Critical Advisor** (.agent/skills/lg-critical-advisor/SKILL.md).

**Implementation:**
- Ask: "Ready to set up for implementation?"
- Use **Viz Architect** (.agent/skills/lg-viz-architect/SKILL.md) to design the visual experience.

## Key Principles
- **Visual Impact** — must be impressive on massive Google Earth displays
- **One question at a time** — don't overwhelm
- **Multiple choice preferred** — easier to answer
- **YAGNI ruthlessly** — remove unnecessary features
- **Explore alternatives** — propose 2-3 approaches before settling
- **Incremental validation** — present design in sections

## Reference Links

- **LG LAB past projects (inspiration)**: https://github.com/LiquidGalaxyLAB
- **GSoC 2026 Ideas**: https://www.liquidgalaxy.eu/2025/11/GSoC2026.html
- **LG Mobile Applications**: https://www.liquidgalaxy.eu/2018/06/mobile-applications.html
- For deeper study → **lg-learning-resources** (.agent/skills/lg-learning-resources/SKILL.md)

## 🔗 Skill Chain

After the design is documented and the student passes the checkpoint, **automatically offer the next stage**:

> *"Great brainstorming session! The design is documented. Now let's design the visualization experience — what will people actually SEE on the multi-screen rig? Ready for the Viz Architect? 🎨"*

If student says "ready" → activate `.agent/skills/lg-viz-architect/SKILL.md`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ashishyesale7) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
