---
name: transcribe-clip
description: Transcribe a video clip using Gemini to get timestamped segments for captions Use when this capability is needed.
metadata:
  author: nuva-lab
---

# Transcribe Clip Skill

Use this skill to generate a timestamped transcript of a video clip for captions.

## Usage

```bash
python skills/transcribe-clip/transcribe.py <video_path> [output_json]

# Example
python skills/transcribe-clip/transcribe.py clip.mp4
python skills/transcribe-clip/transcribe.py clip.mp4 transcript.json
```

## Output Format

```json
{
  "segments": [
    {"start": 0.0, "end": 3.5, "text": "First sentence of dialogue."},
    {"start": 3.5, "end": 7.2, "text": "Second sentence continues here."}
  ],
  "full_text": "Complete transcript..."
}
```

## Notes

- Uses Gemini 3 Pro for transcription
- Timestamps are in seconds relative to clip start
- Segments are roughly sentence-level for readability

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/nuva-lab) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
