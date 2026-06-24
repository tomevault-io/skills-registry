---
name: blucli
description: BluOS CLI (blu) for discovery, playback, grouping, and volume control of Bluesound and NAD speakers. Use when the user wants to play music, stream audio, control speakers, adjust volume, group or ungroup Bluesound players, search TuneIn radio, or manage multi-room streaming setups. Use when this capability is needed.
metadata:
  author: elizaos
---

# blucli (blu)

Use `blu` to control Bluesound/NAD players.

Quick start

- `blu devices` (pick target)
- `blu --device <id> status`
- `blu play|pause|stop`
- `blu volume set 15`

Target selection (in priority order)

- `--device <id|name|alias>`
- `BLU_DEVICE`
- config default (if set)

Common tasks

- Grouping: `blu group status|add|remove`
- TuneIn search/play: `blu tunein search "query"`, `blu tunein play "query"`

Prefer `--json` for scripts. Confirm the target device before changing playback.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/elizaos) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
