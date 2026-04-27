---
name: lyria
description: Lyria RealTime - AI music generation and control Use when this capability is needed.
metadata:
  author: who-visions
---

# Lyria Music Skill

Real-time AI music generation using Gemini Lyria RealTime.

## Features

- **Real-time Generation**: Continuous streaming music
- **Interactive Control**: Steer music with prompts
- **Genre/Mood Control**: Adjust BPM, scale, density

## Usage

```python
from rhea_noir.skills.lyria.actions import skill as lyria

# Start a music session
session = lyria.start_session(
    prompts=["minimal techno", "deep bass"],
    bpm=120
)

# Steer the music
lyria.update_prompts(session, ["piano", "ambient"])

# Stop
lyria.stop(session)
```

## Prompt Examples

- **Instruments**: Piano, Guitar, 808 Hip Hop Beat, Synth Pads
- **Genres**: Techno, Lo-Fi Hip Hop, Jazz Fusion, Dubstep
- **Moods**: Chill, Upbeat, Ethereal, Funky

## Output

- Format: 16-bit PCM
- Sample Rate: 48kHz
- Channels: Stereo

> [!NOTE]
> Experimental model. Instrumental only.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/who-visions) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
