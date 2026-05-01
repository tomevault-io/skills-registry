---
name: volcengine-ai-video-generation
description: AI video generation workflow on Volcengine. Use when users need text-to-video, image-to-video, generation parameter tuning, or async task troubleshooting for video jobs. Use when this capability is needed.
metadata:
  author: openclaw
---

# volcengine-ai-video-generation

Run video generation jobs with deterministic task submission and status polling.

## Execution Checklist

1. Confirm input mode (text-to-video or image-to-video).
2. Set duration, resolution, fps, and style constraints.
3. Submit task and poll status until completion.
4. Return final video URL/path and task metadata.

## Reliability Rules

- Always log task ID for retries.
- Use bounded polling intervals.
- Surface failure reason and rerun suggestions.

## References

- `references/sources.md`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
