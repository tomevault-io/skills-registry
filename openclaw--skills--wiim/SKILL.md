---
name: wiim
description: Control WiiM audio devices (play, pause, stop, next, prev, volume, mute, play URLs, presets). Use when the user wants to control music playback, adjust volume, discover WiiM/LinkPlay speakers on the network, or play audio from a URL on a WiiM device. Use when this capability is needed.
metadata:
  author: openclaw
---

# WiiM CLI

Control WiiM and LinkPlay audio devices from the command line.

## Installation

```bash
# Install globally
uv tool install wiim-cli

# Or run directly without installing
uvx --from wiim-cli wiim --help
```

Requires Python >=3.11.

## Quick Reference

All commands accept `--host <ip>` to target a specific device. If omitted and only one device is on the network, it auto-discovers.

### Discovery

```bash
wiim discover                    # Find devices on the network
```

### Playback

```bash
wiim status                      # Show what's playing
wiim play                        # Resume
wiim pause                       # Pause
wiim stop                        # Stop
wiim next                        # Next track
wiim prev                        # Previous track
wiim seek 90                     # Seek to 1:30
wiim shuffle true                # Enable shuffle
```

### Volume

```bash
wiim volume                      # Show current volume
wiim volume 50                   # Set to 50%
wiim mute                        # Mute
wiim unmute                      # Unmute
```

### Play Media

```bash
wiim play-url "https://example.com/stream.mp3"     # Play a URL
wiim play-preset 1                                   # Play saved preset #1
```

## Notes

- The WiiM must be on the same local network as the machine running the CLI.
- Discovery uses SSDP/UPnP — may not work across subnets/VLANs.
- Spotify, AirPlay, and other streaming services are controlled from their own apps. Once playing on the WiiM, this CLI can pause/play/skip/adjust volume.
- `play-url` works with direct audio URLs (MP3, FLAC, M3U streams, etc.).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
