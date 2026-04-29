---
name: mlx-stt
description: Speech-To-Text with MLX (Apple Silicon) and GLM-ASR-Nano-2512 locally. Use when this capability is needed.
metadata:
  author: demerzels-lab
---

# ⚠️ Deprecation Notice

This skill is **deprecated** and will no longer receive updates.

Please use **mlx-audio-server** instead, which replaces this skill and provides improved functionality.

# MLX STT

Speech-To-Text/ASR/Transcribe with MLX (Apple Silicon) and GLM-ASR-Nano-2512 locally.

Free and Accurate. No api key required. No server required.

## Requirements

- `mlx`: macOS with Apple Silicon
- `brew`: used to install deps if not available

## Installation

```bash
bash ${baseDir}/install.sh
```
This script will use `brew` to install these cli tools if not available:
- `ffmpeg`: convert audio format when needed
- `uv`: install python package and run python script
- `mlx_audio`: do the real job

## Usage

To transcribe an audio file, run the `mlx-stt.py` script:

```bash
uv run  ${baseDir}/mlx-stt.py <audio_file_path>
```

- When first run, it will download model from Hugging Face, default: `mlx-community/GLM-ASR-Nano-2512-8bit`, 2.5GB ish.
- The transcript result will be printed to stdout.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/demerzels-lab) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
