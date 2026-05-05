---
name: douyin
description: Download Douyin (抖音) videos. Use when user wants to: (1) download Douyin videos, (2) get video info (title, author, stats). Supports short links (v.douyin.com) and full URLs. Use when this capability is needed.
metadata:
  author: neversight
---

# Douyin Skill

Download videos from Douyin using browser automation.

## Setup (One-Time)

```bash
python -m nodriver_kit.tools.login_interactive --url https://www.douyin.com --profile douyin
```

## Download Video

```bash
python scripts/download.py "https://v.douyin.com/xxx"
python scripts/download.py "https://v.douyin.com/xxx" --info-only
python scripts/download.py "https://v.douyin.com/xxx" --output ./videos
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
