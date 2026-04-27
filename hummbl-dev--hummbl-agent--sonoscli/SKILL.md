---
name: sonoscli
description: Control Sonos speakers (discover/status/play/volume/group). Use when this capability is needed.
metadata:
  author: hummbl-dev
---

# Sonos CLI

Use `sonos` to control Sonos speakers on the local network.

Quick start

- `sonos discover`
- `sonos status --name "Kitchen"`
- `sonos play|pause|stop --name "Kitchen"`
- `sonos volume set 15 --name "Kitchen"`

Common tasks

- Grouping: `sonos group status|join|unjoin|party|solo`
- Favorites: `sonos favorites list|open`
- Queue: `sonos queue list|play|clear`
- Spotify search (via SMAPI): `sonos smapi search --service "Spotify" --category tracks "query"`

Notes

- If SSDP fails, specify `--ip <speaker-ip>`.
- Spotify Web API search is optional and requires `SPOTIFY_CLIENT_ID/SECRET`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hummbl-dev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
