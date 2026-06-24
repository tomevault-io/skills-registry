---
name: spotify-player
description: Control Spotify playback via spotify_player CLI or Spotify Web API. Use when this capability is needed.
metadata:
  author: kody-w
---

# Spotify Player

Control Spotify playback from the command line.

## Play/Pause

```bash
spotify_player playback play
spotify_player playback pause
```

## Skip Track

```bash
spotify_player playback next
spotify_player playback previous
```

## Search

```bash
spotify_player search --type track "Bohemian Rhapsody"
```

## Now Playing

```bash
spotify_player get --key playback
```

## Queue

```bash
spotify_player playback queue --uri spotify:track:TRACK_ID
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kody-w) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
