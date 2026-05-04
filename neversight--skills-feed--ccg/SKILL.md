---
name: ccg
description: AI Course Content Generator - Generate complete online courses with Gemini API. Triggers on "create course", "generate lesson", "course content", "ccg", "/ccg". Use when this capability is needed.
metadata:
  author: neversight
---

# AI Course Content Generator (CCG)

**Version**: 1.0.0
**Location**: `/Users/shunsukehayashi/dev/ai-course-content-generator-v2`

---

## Triggers

| Trigger | Examples |
|---------|----------|
| Course creation | "create course", "generate course", "/ccg" |
| Development | "ccg dev", "start course generator" |
| Build | "ccg build", "build course app" |

---

## Quick Commands

```bash
# Development
cd /Users/shunsukehayashi/dev/ai-course-content-generator-v2 && npm run dev

# Production build
cd /Users/shunsukehayashi/dev/ai-course-content-generator-v2 && npm run build

# Electron dev
cd /Users/shunsukehayashi/dev/ai-course-content-generator-v2 && npm run electron:dev

# Electron build
cd /Users/shunsukehayashi/dev/ai-course-content-generator-v2 && npm run electron:build
```

---

## Key Capabilities

1. **Course Structure Generation** - JSON curriculum generation
2. **Lesson Script Generation** - Customizable narration scripts
3. **Text-to-Speech** - Gemini TTS audio generation
4. **Slide Generation** - Graphic recording style
5. **Video Rendering** - WebCodecs API MP4 creation
6. **Bulk Export** - ZIP download of all assets

---

## Architecture

```
Vision Panel → Structure → Content Pipeline → Export
     ↓              ↓              ↓            ↓
 Image/PDF/URL  JSON Structure  Slides/Audio  MP3/MP4/ZIP
```

---

## Key Files

| File | Purpose |
|------|---------|
| `services/geminiService.ts` | Gemini API calls + retry logic |
| `templates/prompts.ts` | Zod schemas + prompt builders |
| `utils/audioUtils.ts` | PCM→MP3 encoding (lamejs) |
| `utils/videoUtils.ts` | MP4 muxing (WebCodecs) |
| `types.ts` | Course structure interfaces |
| `constants.ts` | Defaults + TTS voice options |

---

## Gemini Models

- **Primary**: `gemini-3-flash-preview` (with thinking)
- **Backup**: `gemini-2.5-flash` (quota fallback)
- **TTS**: `gemini-2.5-flash-preview-tts`
- **Image**: `gemini-3-pro-image-preview`

---

## Environment

- `GEMINI_API_KEY` required in `.env`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
