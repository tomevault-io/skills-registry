---
name: feature
description: Plan and implement a feature from the game design doc Use when this capability is needed.
metadata:
  author: pjpj42
---

# Implement Feature

Implement a specific feature from the GridRacer design document.

## Arguments
`$ARGUMENTS` - Feature name (e.g., "collision detection", "lap counting", "move preview")

## Process

### 1. Research
- Read `docs/GridRacer Concept.md` for specifications
- Find relevant sections for the feature
- Identify dependencies and prerequisites

### 2. Plan
- Break into subtasks
- Identify files to modify
- Design data structures if needed
- Consider edge cases from design doc

### 3. Implement
- Write code following project conventions
- Use value types (structs) for game state
- Integer-only coordinates
- Follow naming conventions

### 4. Test
- Add unit tests for game logic
- Run `/test` to verify
- Run `/play-test` for visual verification

### 5. Document
- Update architecture.md if needed
- Add to known-issues.md if incomplete

## Feature Checklist from Design Doc

- [ ] Grid rendering
- [ ] Movement model (position + velocity + acceleration)
- [ ] 9 acceleration options UI
- [ ] Path checking (Bresenham's algorithm)
- [ ] Collision detection
- [ ] Crash handling (life lost, respawn)
- [ ] Lap counting
- [ ] Win conditions
- [ ] AI opponent
- [ ] Move preview markers
- [ ] Velocity vector display

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/pjpj42) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
