---
name: basic-room-navigation
description: Use this skill when the objective involves implementing or improving room definitions, navigation between rooms, exits, or win/lose conditions.
metadata:
  author: megazear7
---

# Skill: Basic Room Navigation

## When to apply
- Adding or changing rooms, directions, or movement logic.
- Implementing win condition or game end states.

## Instructions
1. Define all rooms in src/game/rooms.ts as a Record<string, Room>.
2. Each room must have: id, name, description, exits (Record<direction, roomId>).
3. GameState must include currentRoom: string.
4. Navigation: Present Inquirer list of available directions from current room's exits.
5. On move: update state.currentRoom, describe new room with chalk.
6. Check win condition after every move (e.g. currentRoom === "treasure").
7. Add Vitest tests for movement and win detection.

## Example
See current src/game/rooms.ts and src/game/engine.ts for reference.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/megazear7) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
