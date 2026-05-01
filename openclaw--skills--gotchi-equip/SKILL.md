---
name: gotchi-equip
description: Equip, unequip, and inspect Aavegotchi wearables on Base via Bankr submissions. Use when this capability is needed.
metadata:
  author: openclaw
---

# gotchi-equip

Manage wearable loadouts for your gotchis.

## Scripts

- `./scripts/equip.sh <gotchi-id> <slot=wearableId> [slot=wearableId...]`
  - Updates selected slots while preserving existing equipped slots.
- `./scripts/unequip-all.sh <gotchi-id>`
  - Sets all 16 wearable slots to `0`.
- `./scripts/show-equipped.sh <gotchi-id>`
  - Shows currently equipped wearables from the Base subgraph.

## Slot names

`body`, `face`, `eyes`, `head`, `left-hand`, `right-hand`, `pet`, `background`

## Safety notes

- Gotchi ID is validated as numeric input.
- API key is resolved from env/systemd/bankr config paths.
- Equip flow fetches current loadout first to avoid accidental unequip of unspecified slots.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
