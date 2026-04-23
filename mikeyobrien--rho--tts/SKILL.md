---
name: tts
description: Text-to-speech on macOS -- make the device speak text aloud. Use for voice announcements, reading content aloud, or accessibility. Use when this capability is needed.
metadata:
  author: mikeyobrien
---

# Text-to-Speech

## Speak text
```bash
say "Hello, this is a test"
```

## Pipe text
```bash
echo "Hello world" | say
```

## Choose a voice
```bash
say -v Alex "Hello"
say -v Samantha "Hello"
say -v Daniel "Hello"
```

## List available voices
```bash
say -v '?'
```

## Adjust rate (words per minute)
```bash
say -r 200 "Speaking faster"
say -r 100 "Speaking slower"
```

## Save to audio file
```bash
say -o output.aiff "Text to save"
say -o output.aiff --data-format=LEF32@22050 "Text to save"
```

Command blocks until speech completes.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mikeyobrien) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
