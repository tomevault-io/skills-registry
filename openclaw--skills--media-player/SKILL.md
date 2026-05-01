---
name: media-player
description: Play audio/video locally on the host Use when this capability is needed.
metadata:
  author: openclaw
---

# Media Player

Play audio/video locally on the host using mpv. Supports local files and remote URLs.

## Commands

```bash
# Play a local file or URL
media-player play "song.mp3"
media-player play "https://example.com/stream.m3u8"

# Pause playback
media-player pause

# Stop playback
media-player stop
```

## Install

```bash
sudo dnf install mpv
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
