---
name: video-generation
description: AI video generation using Google Veo or OpenAI Sora. Use when user wants to generate, create, or make videos from text prompts. Use when this capability is needed.
metadata:
  author: thrownlemon
---

# AI Video Generation

When the user wants to generate videos from text descriptions, delegate to the `video-generator` subagent.

## When to Use

Use this skill when the user:
- Asks to "generate a video" or "create a video"
- Wants to "animate" something
- Mentions "video of [something]"
- Wants AI video generation from a prompt
- Asks for text-to-video conversion

## What the Subagent Handles

The `video-generator` subagent will:

1. **Check API Keys**
   - Verify GOOGLE_API_KEY (for Veo)
   - Verify OPENAI_API_KEY (for Sora)
   - Guide setup if needed

2. **Select Provider**
   - Smart routing based on request
   - Consider duration and resolution needs
   - Use available API keys

3. **Generate Video**
   - Submit to selected API
   - Poll for completion
   - Download and save result

4. **Report Progress**
   - Show generation status
   - Report completion time
   - Provide output path

## Supported Providers

**Google Veo** (via Gemini API):
| Model | Full ID | Status | Audio |
|-------|---------|--------|-------|
| Veo 3.1 | `veo-3.1-generate-preview` | Preview | Yes |
| Veo 3.1 Fast | `veo-3.1-fast-generate-preview` | Preview | Yes |
| Veo 2.0 | `veo-2.0-generate-001` | Stable | No |

- Duration: 4, 6, 8 seconds
- Resolution: 720p, 1080p, 4K
- Aspect: 16:9, 9:16

**OpenAI Sora**:
| Model | ID | Use Case |
|-------|-----|----------|
| Sora 2 | `sora-2` | Fast iteration, exploration |
| Sora 2 Pro | `sora-2-pro` | Production quality |

Snapshot variants available: `sora-2-2025-10-06`, `sora-2-2025-12-08`, `sora-2-pro-2025-10-06`

- Duration: 4, 8, 12 seconds
- Aspect: 16:9, 9:16, 1:1
- Audio: Included in output

## Default Options

| Option | Veo Default | Sora Default |
|--------|-------------|--------------|
| Duration | 6 seconds | 8 seconds |
| Aspect | 16:9 | 16:9 |
| Resolution | 1080p | 1920x1080 |
| Model | veo-3.1-generate-preview | sora-2 |

**Provider Selection**: Auto-selected based on:
1. Available API keys
2. Requested features (e.g., 12s duration requires Sora)
3. User preference if specified

## Example Triggers

- "Generate a video of a sunset over mountains"
- "Create a video showing a robot dancing"
- "Make a video of autumn leaves falling"
- "I need a video of abstract flowing colors"
- "Animate this scene: a cat playing with yarn"
- "Can you create a short video of fireworks?"

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/thrownlemon) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
