---
name: podcastifier
description: Turn incoming text (email/newsletter) into a short TTS podcast with chunking + ffmpeg concat. Use when this capability is needed.
metadata:
  author: openclaw
---

# podcastifier

Turn incoming text (email/newsletter) into a short TTS podcast with chunking + ffmpeg concat.

## Features
- Parses plain text/HTML input and extracts story bullets.
- Generates TTS per chunk (char limit safe), concatenates via ffmpeg.
- Outputs mp3 with intro/outro.

## Usage
```bash
python podcastify.py --input newsletter.txt --voice "elevenlabs" --out briefing.mp3
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
