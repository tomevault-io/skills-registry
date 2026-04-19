---
name: juicer
description: | Use when this capability is needed.
metadata:
  author: mojuicex
---

# Juicer - Juice Knowledge Pipeline

A workflow for capturing game feel research and systematically implementing it across all Wojak.ink games.

## Workflow Overview

```
┌─────────────────┐     ┌──────────────────┐     ┌─────────────────┐
│  New Learning   │ ──▶ │  Master Playbook │ ──▶ │  Game Updates   │
│  (research)     │     │  (single source) │     │  (15 games)     │
└─────────────────┘     └──────────────────┘     └─────────────────┘
```

## Commands

| Command | Action |
|---------|--------|
| `/juicer add` | Add new learning to playbook |
| `/juicer status` | Show implementation status across games |
| `/juicer propagate` | Update game guides from playbook |
| `/juicer review` | Review recent additions for quality |

## Workflow 1: Add New Learning

When user shares new juice research (from conversation, article, video, etc.):

### Step 1: Categorize the Learning
Identify which playbook section it belongs to:
- Sound Design
- Haptic Feedback
- Visual Juice (particles, animations)
- Screen Effects (shake, flash, vignette)
- Combo/Streak Systems
- Death Sequences
- Near-Miss Feedback
- Time Dilation
- Camera Effects
- Mobile Considerations
- *New section needed?*

### Step 2: Format the Learning
```markdown
### [Learning Title]

**Source:** [Where this came from - conversation, article, game reference]
**Date Added:** [YYYY-MM-DD]
**Priority:** [P0-P2]

**The Pattern:**
[Concise description of the technique]

**Implementation:**
```typescript
// Code example
```

**When to Use:**
- [Context 1]
- [Context 2]

**Games to Update:** [List affected games or "All"]
```

### Step 3: Add to Playbook
1. Read `docs/game-juice-playbook.md`
2. Find appropriate section
3. Add formatted learning
4. Update "Last Updated" date in header

### Step 4: Update Tracker
Add entry to `docs/juice-implementation-tracker.md`:
```markdown
| Learning | Date | Playbook | FO | BP | CD | OS | BB | WW | OS | MM | OP | WR | OJ | KG |
|----------|------|----------|----|----|----|----|----|----|----|----|----|----|----|----|
| [Name]   | Date | ✅       | ⏳ | ⏳ | ⏳ | ⏳ | ⏳ | ⏳ | ⏳ | ⏳ | ⏳ | ⏳ | ⏳ | ⏳ |
```

Legend: ✅ Done | ⏳ Pending | ➖ N/A | 🔄 In Progress

---

## Workflow 2: Check Status

Show implementation progress across all games:

### Read Tracker
```bash
# Read the tracker file
cat docs/juice-implementation-tracker.md
```

### Generate Status Report
```
## Juice Implementation Status

### Recent Additions (Last 30 Days)
- [Learning 1]: 3/12 games updated
- [Learning 2]: 12/12 games updated ✅

### Games Needing Updates
| Game | Pending Items |
|------|---------------|
| FlappyOrange | 0 (fully juiced) |
| BlockPuzzle | 2 pending |
| CitrusDrop | 3 pending |
...

### Priority Queue
1. [P0] Death shake on BlockPuzzle
2. [P1] Near-miss sound on CitrusDrop
3. [P2] Combo escalation on OrangeSnake
```

---

## Workflow 3: Propagate to Games

Update game-specific implementation guides from master playbook:

### Game Implementation Guides
| Game | Guide Location |
|------|----------------|
| FlappyOrange | `docs/FLAPPY-ORANGE-JUICE-IMPLEMENTATION.md` |
| BlockPuzzle | `docs/games/block-puzzle-juice.md` |
| CitrusDrop | `docs/games/citrus-drop-juice.md` |
| OrangeSnake | `docs/games/orange-snake-juice.md` |
| BrickBreaker | `docs/games/brick-breaker-juice.md` |
| WojakWhack | `docs/games/wojak-whack-juice.md` |
| OrangeStack | `docs/games/orange-stack-juice.md` |
| MemoryMatch | `docs/games/memory-match-juice.md` |
| OrangePong | `docs/games/orange-pong-juice.md` |
| WojakRunner | `docs/games/wojak-runner-juice.md` |
| OrangeJuggle | `docs/games/orange-juggle-juice.md` |
| KnifeGame | `docs/games/knife-game-juice.md` |

### Propagation Steps
1. Read new learning from playbook
2. For each applicable game:
   - Read game's juice guide
   - Add game-specific implementation notes
   - Mark as 🔄 in tracker
3. After code implementation:
   - Mark as ✅ in tracker

---

## Workflow 4: Review Quality

Periodically review playbook for:

### Checklist
- [ ] No duplicate patterns across sections
- [ ] All code examples are tested
- [ ] Priority levels are accurate
- [ ] Outdated patterns marked or removed
- [ ] Cross-references are valid
- [ ] Implementation tracker is current

---

## Files Managed by Juicer

| File | Purpose |
|------|---------|
| `docs/game-juice-playbook.md` | Master playbook (single source of truth) |
| `docs/juice-implementation-tracker.md` | Progress across all games |
| `docs/FLAPPY-ORANGE-JUICE-IMPLEMENTATION.md` | Flappy Orange specific guide |
| `docs/games/*-juice.md` | Other game-specific guides |
| `src/lib/juice/` | Reusable code libraries |

---

## Quick Add Template

For fast capture during conversation:

```markdown
## Quick Juice Note

**What:** [Brief description]
**Why it works:** [Psychology/feel reason]
**Code hint:** [Rough implementation idea]
**Priority:** P0/P1/P2
**Applies to:** [Games or "All"]

---
To be formatted and added to playbook later.
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mojuicex) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
