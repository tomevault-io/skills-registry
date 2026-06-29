---
name: qwen-asr
description: >- Use when this capability is needed.
metadata:
  author: huanglizhuo
---

# qwen-asr

Local, CPU-only speech-to-text powered by Qwen3-ASR. No API key or cloud needed.

- Source code: [huanglizhuo/QwenASR](https://github.com/huanglizhuo/QwenASR)
- Based on: [antirez/qwen-asr](https://github.com/antirez/qwen-asr) (original C implementation)

## Install

Run the install script to download the pre-built binary and model:

```bash
bash {baseDir}/scripts/install.sh
```

This will:
1. Download the `qwen-asr` binary for your platform from GitHub Releases
2. Download the `qwen3-asr-0.6b` model (~1.5 GB) from HuggingFace

## Usage

### Transcribe an audio file

```bash
bash {baseDir}/scripts/transcribe.sh <audio-file>
```

Supports any audio format: wav, mp3, m4a, ogg, flac, opus, webm, aac, etc.
Non-WAV files are automatically converted via `ffmpeg` (must be installed).

Or call `qwen-asr` directly (WAV only):

```bash
qwen-asr -d ~/.openclaw/tools/qwen-asr/qwen3-asr-0.6b -i <audio-file> --silent
```

### From stdin

```bash
cat audio.wav | qwen-asr -d ~/.openclaw/tools/qwen-asr/qwen3-asr-0.6b --stdin --silent
```

### Common parameters

| Flag | Description |
|------|-------------|
| `--silent` | Print only transcription text (no progress) |
| `--language <lang>` | Force language (e.g., `zh`, `en`) |
| `-S <seconds>` | Segmented mode — split audio into chunks |
| `--stream` | Streaming mode — process audio in real time |
| `--stdin` | Read audio from stdin |

### Model path

Default model directory: `~/.openclaw/tools/qwen-asr/qwen3-asr-0.6b`

---
> Source: [huanglizhuo/QwenASR](https://github.com/huanglizhuo/QwenASR) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-29 -->
