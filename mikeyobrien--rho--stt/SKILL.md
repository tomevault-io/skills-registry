---
name: stt
description: Speech-to-text — transcribe voice to text using device microphone. Use for voice commands, dictation, or hands-free input. Use when this capability is needed.
metadata:
  author: mikeyobrien
---

# Speech-to-Text

## Basic usage
```bash
termux-speech-to-text
```

Opens a speech recognition dialog. Returns transcribed text as JSON when user stops speaking.

## Output format
```json
{"result": "the spoken text here"}
```

## Notes
- Requires microphone permission
- Uses Google speech recognition (needs network)
- User must tap the mic or speak; dialog auto-closes on silence
- Returns empty result if canceled or no speech detected

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mikeyobrien) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
