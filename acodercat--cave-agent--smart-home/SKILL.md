---
name: smart-home
description: Control smart home devices including lights, doors, and TV Use when this capability is needed.
metadata:
  author: acodercat
---

# Smart Home Skill

Use this skill to check status and control smart home devices.

## Injected Functions

- `get_status(device_id=None)`: Get status of a device, or all devices if no ID
- `set_light(device_id, state, brightness=100)`: Turn a light on/off with brightness (0-100)
- `set_door(device_id, state)`: Lock or unlock a door
- `set_tv(state, channel=None, volume=None)`: Control TV power, channel, and volume

## Device IDs

- Lights: `living_room_light`, `bedroom_light`, `kitchen_light`
- Doors: `front_door`, `garage_door`
- TV: `tv`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/acodercat) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
