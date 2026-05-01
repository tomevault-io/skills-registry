---
name: clawcut
description: >- Use when this capability is needed.
metadata:
  author: openclaw
---

# ClawCut 🦞✂️

AI short video generator: topic → script → video with native voice.

## Pipeline

1. **Script generation** — Gemini 3 Pro generates 9-scene screenplay (Chinese narration + English visual descriptions)
2. **Nine-grid image** — Gemini 3 Pro Image creates character consistency reference (supports up to 14 input images)
3. **Video generation** — Veo 3.1 generates 9 video clips concurrently with native Chinese speech
4. **Post-processing** — Silence trimming + ffmpeg concat into final video

## Prerequisites

- Google Cloud project with Vertex AI enabled
- Service account JSON with Vertex AI User role
- ffmpeg binary installed
- Python 3.11+

## Setup

```bash
# Create project from skill scripts
mkdir -p clawcut && cp scripts/*.py scripts/requirements.txt clawcut/
cp assets/.env.example clawcut/.env
cd clawcut

# Create venv and install deps
python3.11 -m venv venv
source venv/bin/activate
pip install -r requirements.txt -i https://pypi.tuna.tsinghua.edu.cn/simple

# Configure environment
# Edit .env with your values:
#   GOOGLE_APPLICATION_CREDENTIALS=path/to/service-account.json
#   VERTEX_PROJECT=your-gcp-project-id
#   VERTEX_LOCATION=us-central1
#   FFMPEG_PATH=/usr/local/bin/ffmpeg
```

## Usage

### Gradio UI
```bash
source venv/bin/activate
python3 app.py
# Opens at http://localhost:7860
```

### Modes
- **Topic mode** — Enter a topic, generate full video from scratch
- **Video imitation** — Upload reference video, analyze style and generate matching content
- **Multi-image reference** — Upload up to 14 images for character consistency

### Models (all Vertex AI paid)
- Script: `gemini-3-pro-preview`
- Image: `gemini-3-pro-image-preview`
- Video: `veo-3.1-generate-001`

## Key Features
- 9-way concurrent video generation (~3 min total)
- Checkpoint/resume (skips existing files)
- Silence trimming (ffmpeg silencedetect)
- Video style imitation from reference
- Up to 14 reference images for character consistency
- All credentials via environment variables (zero hardcoded secrets)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
