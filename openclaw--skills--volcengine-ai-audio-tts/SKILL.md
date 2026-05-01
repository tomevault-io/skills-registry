---
name: volcengine-ai-audio-tts
description: Text-to-speech generation on Volcengine audio services. Use when users need narration, multi-language speech output, voice selection, or TTS troubleshooting. Use when this capability is needed.
metadata:
  author: openclaw
---

# volcengine-ai-audio-tts

Synthesize speech from text with voice, language, and format controls.

## Execution Checklist

1. Confirm input text, language, and target voice.
2. Set output format and sample rate.
3. Execute TTS request and poll if async.
4. Return audio URL/path and reproducible parameters.

## Output Rules

- Prefer stable audio formats (`mp3` or `wav`).
- Keep text chunks short for long passages.
- Surface duration and file size when possible.

## References

- `references/sources.md`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
