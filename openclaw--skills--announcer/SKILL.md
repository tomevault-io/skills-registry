---
name: announcer
description: Announce text throughout the house via AirPlay speakers using Airfoil + ElevenLabs TTS. Use when this capability is needed.
metadata:
  author: openclaw
---

# Announcer

Play TTS announcements through AirPlay speakers via Airfoil and ElevenLabs.

## How It Works

1. Generate speech via ElevenLabs (high-quality opus → stereo MP3)
2. Connect to AirPlay speakers via Airfoil
3. Play an optional chime (gong) followed by the announcement
4. Disconnect speakers after playback

## Setup

See [SETUP.md](SETUP.md) for prerequisites and setup instructions.

## Usage

```bash
# Announce to all configured speakers
python3 skills/announcer/scripts/announce.py "Dinner is ready!"

# Announce to specific speakers only
python3 skills/announcer/scripts/announce.py "Wake up!" --speakers "Kids Room"

# Skip the chime
python3 skills/announcer/scripts/announce.py "Quick note" --no-gong
```

## File Structure

```
announcer/
├── SKILL.md
├── assets/
│   └── gong_stereo.mp3      # Announcement chime
└── scripts/
    └── announce.py           # Main announcement script
```

User config (not part of skill):
```
~/clawd/announcer/
└── config.json               # Speaker list, voice, audio settings
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
