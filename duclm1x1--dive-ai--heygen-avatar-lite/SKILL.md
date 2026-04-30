---
name: heygen-avatar-lite
description: Create AI digital human videos with HeyGen API. Free starter guide. Use when this capability is needed.
metadata:
  author: duclm1x1
---

# 🎬 HeyGen AI Avatar Video (Lite)

Create professional AI-generated videos with your own digital human avatar!

## 🎯 What You'll Build

- Generate videos with AI avatars speaking any text
- Support for multiple languages
- Portrait (9:16) and Landscape (16:9) formats
- Custom voice cloning integration

## 📋 Prerequisites

1. **HeyGen Account** (Creator plan or above)
   - Sign up: https://heygen.com
   - Get API key from Settings → API

2. **Custom Avatar** (optional)
   - Upload training video to create your digital twin
   - Or use HeyGen's stock avatars

## 🏗️ Architecture

```
┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│   Your App  │────▶│  HeyGen API │────▶│   Video     │
│  (trigger)  │     │  (generate) │     │   Output    │
└─────────────┘     └─────────────┘     └─────────────┘
        │                  │
        ▼                  ▼
   ┌─────────┐      ┌─────────────┐
   │  Text   │      │   Avatar +  │
   │  Input  │      │   Voice     │
   └─────────┘      └─────────────┘
```

## 🚀 Quick Start

### Step 1: Get Your API Key

```bash
HEYGEN_API_KEY="your_api_key_here"
```

### Step 2: List Available Avatars

```bash
curl -X GET "https://api.heygen.com/v2/avatars" \
  -H "X-Api-Key: $HEYGEN_API_KEY" | jq '.data.avatars[:5]'
```

### Step 3: List Available Voices

```bash
curl -X GET "https://api.heygen.com/v2/voices" \
  -H "X-Api-Key: $HEYGEN_API_KEY" | jq '.data.voices[:5]'
```

### Step 4: Generate a Video

```bash
curl -X POST "https://api.heygen.com/v2/video/generate" \
  -H "X-Api-Key: $HEYGEN_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "video_inputs": [{
      "character": {
        "type": "avatar",
        "avatar_id": "YOUR_AVATAR_ID",
        "avatar_style": "normal"
      },
      "voice": {
        "type": "text",
        "input_text": "Hello! This is my AI avatar speaking.",
        "voice_id": "YOUR_VOICE_ID"
      }
    }],
    "dimension": {
      "width": 1280,
      "height": 720
    }
  }'
```

### Step 5: Check Video Status

```bash
VIDEO_ID="your_video_id"
curl -X GET "https://api.heygen.com/v1/video_status.get?video_id=$VIDEO_ID" \
  -H "X-Api-Key: $HEYGEN_API_KEY"
```

## 📐 Video Dimensions

| Format | Dimensions | Use Case |
|--------|------------|----------|
| Landscape | 1280x720 | YouTube, Website |
| Portrait | 720x1280 | TikTok, Reels, Shorts |
| Square | 1080x1080 | Instagram |

## 💰 Cost Estimate

| Plan | Price | Credits |
|------|-------|---------|
| Creator | $29/month | 15 min/month |
| Business | $89/month | 30 min/month |
| Per-minute overage | ~$1-2/min | - |

## ⚠️ Limitations of Lite Version

- Basic API guide only
- No automation scripts
- No error handling
- No subtitle integration
- Community support only

## 🚀 Want More?

**Premium Version** includes:
- ✅ Complete Python generation script
- ✅ Automatic video download
- ✅ Portrait + Landscape presets
- ✅ Integration with ZapCap subtitles
- ✅ Batch video generation
- ✅ LINE/Telegram delivery integration
- ✅ Priority support

Get it on **Virtuals ACP**: Find @LittleLobster

---

Made with 🦞 by LittleLobster

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/duclm1x1) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
