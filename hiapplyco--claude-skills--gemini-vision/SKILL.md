---
name: gemini-vision
description: Generate images and videos using Gemini CLI's vision extension. Use for image generation with Nano Banana (gemini-2.5-flash-image), video generation with Veo 3, webcam capture, and image-to-image transformations. Invokes Gemini CLI commands and returns file paths. Use when this capability is needed.
metadata:
  author: hiapplyco
---

# Gemini Vision Skill

Generate AI images and videos by invoking Gemini CLI's vision extension. This skill provides access to:
- **Nano Banana** (gemini-2.5-flash-image) - Image generation and transformation
- **Veo 3** (veo-3.0-generate-001) - Video generation from images
- **Webcam capture** - Live frame capture for AI processing

## Prerequisites

1. **Gemini CLI**: Must be installed and configured
2. **Vision Extension**: Install via:
   ```bash
   gemini extensions install vision
   ```
3. **API Key**: Set `GEMINI_API_KEY` environment variable

## When to Use This Skill

Use this skill when the user asks to:
- Generate images from text prompts
- Transform or reimagine existing images
- Create AI-generated videos from images
- Capture webcam frames for AI processing
- Create "nano banana" style images
- Generate Veo videos

## Available Operations

### 1. Image Generation (Nano Banana)

Generate images from text prompts or transform existing images.

**Command Pattern:**
```bash
gemini -p "/vision:banana prompt=\"Your creative prompt here\" n=1 out_dir=./output"
```

**Parameters:**
| Parameter | Default | Description |
|-----------|---------|-------------|
| `prompt` | Required | Creative description of desired image |
| `n` | 1 | Number of images to generate |
| `out_dir` | "." | Output directory for images |
| `model` | gemini-2.5-flash-image | Image generation model |

**Models Available:**
- `gemini-2.5-flash-image` (default, recommended)
- `gemini-3-pro-image-preview` (newer, experimental)

### 2. Video Generation (Veo 3)

Generate short videos from images or prompts.

**Command Pattern:**
```bash
gemini -p "/vision:veo prompt=\"Animate this scene\" aspect_ratio=16:9 out_dir=./output"
```

**Parameters:**
| Parameter | Default | Description |
|-----------|---------|-------------|
| `prompt` | Required | Animation/motion description |
| `aspect_ratio` | "16:9" | Video aspect ratio (16:9 or 9:16) |
| `resolution` | auto | Video resolution (e.g., "1080p") |
| `negative_prompt` | "" | What to avoid in video |
| `veo_model` | veo-3.0-generate-001 | Video model |

### 3. Webcam Capture + AI

Capture from webcam and process with AI.

```bash
# Start camera
gemini -p "/vision:start"

# Capture and transform
gemini -p "/vision:banana prompt=\"Transform into oil painting\""

# Stop camera
gemini -p "/vision:stop"
```

## Instructions for Claude

When the user requests image or video generation:

1. **Determine the operation type:**
   - Text-to-image → Use `/vision:banana`
   - Image transformation → Use `/vision:banana` with input image
   - Image-to-video → Use `/vision:veo`
   - Webcam capture → Use `/vision:capture` or `/vision:banana`

2. **Construct the Gemini CLI command:**
   ```bash
   gemini -p "/vision:<command> prompt=\"<user prompt>\" <params>"
   ```

3. **Execute via Bash tool:**
   - Run the command
   - Capture the output paths
   - Report success and file locations to user

4. **Handle output:**
   - Images saved as `banana_*.png` or `banana_*.jpg`
   - Videos saved as `veo_*.mp4`
   - Return the file paths to the user

## Example Workflows

### Generate a Single Image

**User:** "Generate an image of a cyberpunk city at sunset"

**Action:**
```bash
gemini -p "/vision:banana prompt=\"A sprawling cyberpunk city at sunset, neon lights reflecting off wet streets, flying cars in the distance, highly detailed, cinematic\" n=1 out_dir=."
```

### Transform an Image

**User:** "Make this photo look like a Studio Ghibli scene" (with image attached)

**Action:**
1. Save the attached image to a temp location
2. Run:
```bash
gemini -p "/vision:banana prompt=\"Transform into Studio Ghibli animation style, soft colors, whimsical atmosphere\" input_paths=['/path/to/image.jpg']"
```

### Generate a Video

**User:** "Create a video of ocean waves"

**Action:**
```bash
gemini -p "/vision:veo prompt=\"Calm ocean waves gently rolling onto a sandy beach, golden hour lighting, peaceful atmosphere\" aspect_ratio=16:9"
```

### Webcam to Art

**User:** "Take a photo of me and make it look like a Renaissance painting"

**Action:**
```bash
# Capture and transform in one step
gemini -p "/vision:banana prompt=\"Transform into a Renaissance oil painting, dramatic lighting, classical composition\""
```

## Output Format

Always report results in this format:

```
## Generated Content

**Type:** Image/Video
**Files:**
- `/path/to/banana_20251227_123456_000.png`

**Prompt Used:** [the prompt]
**Model:** gemini-2.5-flash-image

To view: Open the file path above or use `open /path/to/file`
```

## Error Handling

Common issues and solutions:

| Error | Solution |
|-------|----------|
| "Camera not found" | Run `/vision:devices` to list cameras |
| "GEMINI_API_KEY not set" | Export the API key in environment |
| "Model not available" | Check model ID spelling |
| "Generation failed" | Try simpler prompt or different model |

## Script Usage (Alternative)

For programmatic access, use the helper script:

```bash
python ~/.claude/skills/gemini-vision/scripts/gemini_vision.py \
  --operation banana \
  --prompt "Your prompt here" \
  --output-dir ./output \
  --count 1
```

Options:
- `--operation`: banana, veo, capture, devices
- `--prompt`: The generation prompt
- `--output-dir`: Where to save files
- `--count`: Number of images (for banana)
- `--aspect-ratio`: For veo (16:9 or 9:16)
- `--model`: Override default model

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hiapplyco) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
