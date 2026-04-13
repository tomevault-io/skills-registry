---
name: video-frames
description: Extract frames or short clips from videos using ffmpeg. Use when the user asks to grab a frame, capture a screenshot from a video, extract a thumbnail, pull a still image from footage, or snapshot a specific timestamp in a video file. Use when this capability is needed.
metadata:
  author: elizaos
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
> Converted and distributed by [TomeVault](https://tomevault.io/claim/elizaos) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
