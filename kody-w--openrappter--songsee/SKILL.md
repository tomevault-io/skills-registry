---
name: songsee
description: Identify songs using audio fingerprinting via Shazam or AudD APIs. Use when this capability is needed.
metadata:
  author: kody-w
---

# SongSee

Identify songs from audio samples.

## Using AudD API

```bash
curl -s -X POST "https://api.audd.io/" \
  -F file=@audio-sample.mp3 \
  -F api_token="$AUDD_API_TOKEN" \
  -F return="apple_music,spotify" | jq '{title: .result.title, artist: .result.artist}'
```

## From URL

```bash
curl -s -X POST "https://api.audd.io/" \
  -F url="https://example.com/audio.mp3" \
  -F api_token="$AUDD_API_TOKEN" | jq '.result'
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kody-w) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
