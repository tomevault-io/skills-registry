---
name: spotify-player
description: Terminal Spotify playback/search via spogo (preferred) or spotify_player. Use when the user asks to play music, search for a song, skip a track, pause playback, check what is currently playing, control Spotify, list audio devices, or manage a Spotify queue from the terminal. Use when this capability is needed.
metadata:
  author: elizaOS
---

# spogo / spotify_player

Use `spogo` **(preferred)** for Spotify playback/search. Use `spotify_player` when `spogo` is unavailable.

Requirements

- Spotify Premium account.
- Either `spogo` or `spotify_player` installed.

spogo setup

- Import cookies: `spogo auth import --browser chrome`

Common CLI commands

- Search: `spogo search track "query"`
- Playback: `spogo play|pause|next|prev`
- Devices: `spogo device list`, `spogo device set "<name|id>"`
- Status: `spogo status`

spotify_player commands

- Search: `spotify_player search "query"`
- Playback: `spotify_player playback play|pause|next|previous`
- Connect device: `spotify_player connect`
- Like track: `spotify_player like`

Notes

- Config folder: `~/.config/spotify-player` (e.g., `app.toml`).
- For Spotify Connect integration, set a user `client_id` in config.
- TUI shortcuts are available via `?` in the app.

---
> Source: [elizaOS/eliza](https://github.com/elizaOS/eliza) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-25 -->
