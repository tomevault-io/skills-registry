---
name: video-frames
description: Extract frames or short clips from videos using ffmpeg. Use when this capability is needed.
metadata:
  author: majiayu000
---

# Video Frames (ffmpeg)

Extract a single frame from a video, or create quick thumbnails for inspection.

## Quick start

First frame:

```bash
{baseDir}/scripts/frame.sh /path/to/video.mp4 --out /tmp/frame.jpg
```

At a timestamp:

```bash
{baseDir}/scripts/frame.sh /path/to/video.mp4 --time 00:00:10 --out /tmp/frame-10s.jpg
```

## Notes

- Prefer `--time` for “what is happening around here?”.
- Use a `.jpg` for quick share; use `.png` for crisp UI frames.

---
> Source: [majiayu000/claude-skill-registry](https://github.com/majiayu000/claude-skill-registry) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-07-07 -->
