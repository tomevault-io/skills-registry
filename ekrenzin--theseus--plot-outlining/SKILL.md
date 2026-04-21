---
name: plot-outlining
description: Structure a complete narrative arc with inciting incident, rising action, turning points, climax, and resolution. Maps character arcs against plot beats. Use when the user wants to outline a plot, structure a story, plan a narrative arc, or mentions plot outline or story structure. Use when this capability is needed.
metadata:
  author: ekrenzin
---

# Plot Outlining

## Purpose

Create a complete plot structure that serves as the blueprint for the entire book. The plot must emerge from character desires colliding with world constraints -- not the other way around.

## Workflow

1. **Read existing materials** -- Read the world bible and all character sheets
2. **Identify the engine** -- What character desire + world constraint creates the central conflict?
3. **Generate the plot outline** following the structure below
4. **Map character arcs** onto plot beats
5. **Write to file** at `books/<book-title>/plot/plot-outline.md`
6. **Review with user**

## Plot Outline Structure

```markdown
# Plot Outline: [BOOK TITLE]

## Premise

One sentence: [CHARACTER] must [ACTION] or else [STAKES].

## Central Conflict

- External conflict: (what is physically happening)
- Internal conflict: (what is psychologically at stake)
- Thematic conflict: (what idea is being tested)

## Act Structure

### Act 1: Setup (approx chapters 1-N)

- **Opening Image**: What does the world look like before the story changes it?
- **Status Quo**: The protagonist's ordinary world and its hidden problems
- **Inciting Incident**: The event that makes the old life impossible
- **Debate/Refusal**: Why the protagonist resists the call
- **Threshold**: The moment of commitment -- no going back

### Act 2A: Complication (approx chapters N-N)

- **New World**: The protagonist encounters unfamiliar territory
- **Fun and Games**: The promise of the premise delivered
- **Allies and Enemies**: Key relationships form or reveal themselves
- **Midpoint**: A major revelation or reversal that raises the stakes
  - (Specify: false victory or false defeat?)

### Act 2B: Escalation (approx chapters N-N)

- **Consequences**: The midpoint fallout changes everything
- **Tightening Noose**: Antagonist pressure increases, options narrow
- **All Is Lost**: The protagonist's lowest point
- **Dark Night of the Soul**: Emotional reckoning with the internal conflict

### Act 3: Resolution (approx chapters N-N)

- **Synthesis**: The protagonist integrates what they have learned
- **Climax**: External and internal conflicts collide in a final confrontation
- **Resolution**: New equilibrium -- how the world has changed
- **Closing Image**: Mirror of the opening image, showing transformation

## Chapter Breakdown

| Ch  | Title (working) | Key Event | POV | Arc Beat |
| --- | --------------- | --------- | --- | -------- |
| 1   |                 |           |     |          |
| 2   |                 |           |     |          |
| ... |                 |           |     |          |

## Character Arc Map

For each major character:
| Character | Want | Need | Arc Start | Midpoint Shift | Arc End |
|-----------|------|------|-----------|----------------|---------|
| | | | | | |

## Subplot Threads

| Subplot | Characters | Connects to Main Plot At | Resolution |
| ------- | ---------- | ------------------------ | ---------- |
|         |            |                          |            |

## Promises and Payoffs

| Promise (setup) | Chapter Planted | Payoff | Chapter Delivered |
| --------------- | --------------- | ------ | ----------------- |
|                 |                 |        |                   |

## Tension Escalation Curve

Describe how tension increases chapter by chapter. No two consecutive chapters should have the same tension level.
```

## Guidelines

- **Character-driven plots**: Every plot event must be caused by a character decision, not by coincidence. Coincidence can start a plot but never resolve one.
- **Escalating stakes**: Each act must raise the stakes. If stakes plateau, the story stalls.
- **Promises and payoffs**: Every setup needs a payoff. Track them explicitly.
- **Subplot integration**: Subplots must connect to the main plot thematically or causally. No orphan subplots.
- **Chapter pacing**: Alternate between high-tension and breathing-room chapters. Never stack three slow chapters or three action chapters in a row without reason.

## Validation Checklist

- [ ] Premise can be stated in one sentence
- [ ] Every act has clear turning points
- [ ] Character arc map is filled in for all major characters
- [ ] Every subplot connects to the main plot
- [ ] Promises and payoffs are tracked
- [ ] No plot events rely on coincidence for resolution
- [ ] Chapter breakdown accounts for all major beats
- [ ] Plot outline is saved to `books/<book-title>/plot/plot-outline.md`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ekrenzin) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
