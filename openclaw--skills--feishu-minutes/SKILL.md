---
name: feishu-minutes
description: Fetch info, stats, transcript, and media from Feishu Minutes. Use when this capability is needed.
metadata:
  author: openclaw
---
# Feishu Minutes (妙记) Skill

Fetch info, stats, transcript, and media from Feishu Minutes.

## Usage

```bash
node skills/feishu-minutes/index.js process <minutes_token> --out <output_dir>
```

- `<minutes_token>`: The token from the Minutes URL (e.g., `mmcn...`).
- `--out`: Optional output directory (defaults to `memory/feishu_minutes/<token>`).

## Output
- `info.json`: Basic metadata.
- `stats.json`: View/Comment stats.
- `subtitle.json`: Raw transcript data.
- `transcript.md`: Readable transcript.
- `media.mp4`: Video/Audio recording.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
