---
name: feishu-voice-assistant
description: Sends voice messages (audio) to Feishu chats using Duby TTS. Use when this capability is needed.
metadata:
  author: openclaw
---

# Feishu Voice Assistant

Generate speech from text using Duby AI and send it as a native voice message (audio) to Feishu.

## Usage

### Send a Voice Message
```bash
node skills/feishu-voice-assistant/index.js --text "Hello, this is a voice message!" --target "$TARGET_USER_ID"
```

### Options
- `--text`: The text to convert to speech.
- `--target`: The Feishu user ID (`ou_...`) or chat ID (`oc_...`).
- `--voice`: (Optional) Duby Voice ID. Default is Xinduo.

## Dependencies
- `duby`: For TTS generation.
- `feishu-common`: For API authentication.
- `form-data`: For file uploads.

## Configuration
Requires `DUBY_API_KEY` and Feishu credentials in `.env`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
