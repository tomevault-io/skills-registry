---
name: add-inventory
description: Use this skill when the objective involves adding items, pickup, dropping, inventory display, or item usage.
metadata:
  author: megazear7
---

# Skill: Add Inventory System

## When to apply
- Any task mentioning inventory, items, pickup, backpack, use item, etc.

## Instructions
1. Extend Zod schemas in src/game/types.ts: ItemSchema and add inventory: z.array(ItemSchema) to GameState.
2. Add items to rooms (e.g. items: string[] or full Item objects).
3. Add Inquirer choices: "Look around", "Take [item]", "Inventory", etc.
4. On "Take": move item from room to player inventory, validate with Zod.
5. Persist inventory in any future save/load.
6. Write tests for pickup, duplicate prevention, inventory listing.
7. Use chalk to highlight items.

## Common pitfalls to avoid
- Forgetting to validate with Zod.parse on state changes.
- Not updating room state when item is taken.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/megazear7) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
