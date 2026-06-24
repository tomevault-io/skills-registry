---
name: video-url-transcriber
description: Transcribe a video/audio URL into timestamped JSON using yt-dlp + ffmpeg + faster-whisper. Use when an agent needs platform-agnostic URL-to-transcript ingestion for downstream analysis. Use when this capability is needed.
metadata:
  author: nativ3ai
---

# Video URL Transcriber Skill

Use this skill when a user asks for transcript extraction from a public media URL (YouTube, X, and any site supported by yt-dlp).

## Preconditions

Install system deps:

```bash
brew install ffmpeg yt-dlp
```

Install Python deps from repo root:

```bash
pip install -r requirements.txt
```

## Minimal workflow

1. Run dependency check:

```bash
python3 skill/video-url-transcriber/scripts/transcribe_url.py --doctor
```

2. Run one-shot URL transcription:

```bash
python3 skill/video-url-transcriber/scripts/transcribe_url.py "https://www.youtube.com/watch?v=..." --model-size small
```

3. Return JSON output with:
- `source_url`
- `platform`
- `title`
- `duration_sec`
- `language`
- `transcript`
- `segments[]` with timestamps and optional words

## API mode (agent workers)

Start API service:

```bash
python3 skill/video-url-transcriber/scripts/run_api.py
```

Transcribe request:

```bash
curl -s http://127.0.0.1:8099/transcribe \
  -H 'content-type: application/json' \
  -d '{
    "url": "https://www.youtube.com/watch?v=...",
    "language": null,
    "model_size": "small",
    "word_timestamps": true,
    "persist_media": false
  }'
```

## Optional auth-gated media

Default policy: do **not** use browser cookies for public URLs.

If and only if the user explicitly approves personal browser/session usage, pass browser cookies with explicit opt-in:

```bash
python3 skill/video-url-transcriber/scripts/transcribe_url.py "<url>" --cookies-from-browser chrome --allow-personal-cookies
```

## Execution rules

- Always normalize audio to mono 16k WAV before ASR.
- Never use browser cookies/keychain data unless user explicitly asks for it.
- Default `model_size` is `small`; raise to `medium`/`large-v3` only when user asks for higher quality.
- Prefer `persist_media=false` unless user explicitly wants artifacts retained.
- If transcription fails, return exact failing stage: `fetch`, `ffmpeg normalize`, or `asr`.

---
> Source: [nativ3ai/universal-video-transcriber](https://github.com/nativ3ai/universal-video-transcriber) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-21 -->
