---
name: openhue
description: Control Philips Hue lights and scenes via the OpenHue CLI. Use when this capability is needed.
metadata:
  author: kody-w
---

# OpenHue

Control Philips Hue lights from the command line.

## List Lights

```bash
openhue get lights
```

## Turn On/Off

```bash
openhue set light "Living Room" --on true
openhue set light "Bedroom" --on false
```

## Set Color and Brightness

```bash
openhue set light "Desk Lamp" --brightness 80 --color "#FF6B35"
```

## Scenes

```bash
# List scenes
openhue get scenes

# Activate a scene
openhue set scene "Movie Night"
```

## Rooms

```bash
openhue get rooms
openhue set room "Office" --on true --brightness 100
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kody-w) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
