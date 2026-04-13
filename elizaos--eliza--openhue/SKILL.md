---
name: openhue
description: Controls Philips Hue smart lights, rooms, and scenes via the OpenHue CLI and a Hue Bridge. Use when the user asks to turn lights on or off, change brightness or color, activate a scene, list rooms, discover bridges, or manage smart lighting. Handles setup, querying light state, and adjusting individual or grouped lights by name or ID. Use when this capability is needed.
metadata:
  author: elizaos
---

# OpenHue CLI

Use `openhue` to control Hue lights and scenes via a Hue Bridge.

Setup

- Discover bridges: `openhue discover`
- Guided setup: `openhue setup`

Read

- `openhue get light --json`
- `openhue get room --json`
- `openhue get scene --json`

Write

- Turn on: `openhue set light <id-or-name> --on`
- Turn off: `openhue set light <id-or-name> --off`
- Brightness: `openhue set light <id> --on --brightness 50`
- Color: `openhue set light <id> --on --rgb #3399FF`
- Scene: `openhue set scene <scene-id>`

Notes

- You may need to press the Hue Bridge button during setup.
- Use `--room "Room Name"` when light names are ambiguous.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/elizaos) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
