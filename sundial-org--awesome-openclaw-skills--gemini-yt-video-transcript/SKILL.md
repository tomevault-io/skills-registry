---
name: gemini-yt-video-transcript
description: Create a verbatim transcript for a YouTube URL using Google Gemini (speaker labels, paragraph breaks; no time codes). Use when the user asks to transcribe a YouTube video or wants a clean transcript (no timestamps). Use when this capability is needed.
metadata:
  author: sundial-org
---

# Gemini YouTube Video Transcript

Create a **verbatim transcript** for a YouTube URL using **Google Gemini**.

**Output format**
- First line: YouTube video title
- Then transcript lines only in the form:

```
Speaker: text
```

**Requirements**
- No time codes
- No extra headings / lists / commentary

## Usage

```bash
python3 {baseDir}/scripts/youtube_transcript.py "https://www.youtube.com/watch?v=..."
```

Options:
- `--out <path>` Write transcript to a specific file (default: auto-named in the workspace `out/` folder).

## Delivery

When chatting: send the resulting transcript as a document/attachment.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sundial-org) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
