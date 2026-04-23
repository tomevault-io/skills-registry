---
name: supermachine
description: Generates AI images for e-learning courses using Supermachine.art. Use when creating course graphics, illustrations, thumbnails, module visuals, educational diagrams, stock photo replacements, or when user mentions "AI image," "generate image," "create visual," "course graphics," "Supermachine," or "illustration for course.
metadata:
  author: webmasterarbez
---

# Supermachine AI Image Generation for E-Learning

Generate professional AI images for course assets using Supermachine.art - an AI image generator with 70+ models based on Stable Diffusion, SDXL, and FLUX.

## Quick Reference

**Platform:** https://supermachine.art
**Documentation:** https://docs.supermachine.art
**Image Generation Time:** Under 15 seconds
**Commercial Use:** Yes, all generated images

## Core Workflow

### 1. Define the Image Need

Before generating, clarify:
- **Purpose**: Thumbnail, illustration, diagram background, hero image
- **Style**: Photorealistic, illustration, flat design, isometric, infographic
- **Subject**: People, objects, concepts, abstract
- **Mood**: Professional, friendly, energetic, calm
- **Aspect Ratio**: 16:9 (video), 1:1 (social), 4:3 (presentation)

### 2. Choose the Right Model

**For Photorealistic Images (People, Offices, Workplaces):**
- **Flux** - Best for realistic images WITH text capability
- **Crystal Clear XL** - Very realistic images
- **Epic Realism** - High-quality realistic photos
- **Super Portrait** - Professional headshots and portraits
- Models tagged "Photorealistic"

**For Illustrations & Graphics:**
- **Supermachine Anime/Comic/Manga** - Stylized illustrations
- **Logos & Icons** - Icon and logo generation
- **Vector art models** - Clean graphics
- **Digital art models** - Stylized visuals

**For Text in Images:**
- **Flux** - Only model that reliably renders text

### 3. Write Effective Prompts

**Prompt Structure:**
```
[Subject], [Style], [Details], [Lighting], [Camera/Composition], [Quality modifiers]
```

**Example Prompts for Course Assets:**

**Professional Headshot for Instructor:**
```
professional business portrait of a confident woman in her 30s,
wearing professional blazer, warm smile, office background with
bokeh, natural lighting, high quality, photorealistic
```

**Course Thumbnail:**
```
young professional looking at laptop with excited expression,
modern office, bright natural lighting, 16:9 aspect ratio,
photorealistic, professional stock photo style
```

**Concept Illustration - Job Interview:**
```
job interview scene, candidate and hiring manager shaking hands,
bright modern office, confident body language, professional
attire, natural lighting, photorealistic
```

**Flat Design Illustration:**
```
flat design illustration of resume document with magnifying glass,
minimalist style, professional blue color scheme, clean vectors,
white background
```

**Infographic Background:**
```
abstract gradient background, soft blue and white, professional,
minimal, clean, suitable for text overlay
```

### 4. Configure Settings

**Essential Settings:**

| Setting | Recommended Value | Purpose |
|---------|-------------------|---------|
| **Model** | Based on image type | See model selection above |
| **Prompt Guidance** | 7-9 | Balance creativity vs. accuracy |
| **Steps** | Default (model-specific) | Quality iterations |
| **Resolution** | Up to 1280×1280 | Higher = more detail |
| **Aspect Ratio** | Match delivery format | 16:9, 1:1, 4:3, etc. |

**Prompt Guidance Scale:**
- **5-6**: More creative, may deviate from prompt
- **7-9**: Balanced (recommended for most uses)
- **10-12**: Strict adherence to prompt

### 5. Use Negative Prompts

**Standard Negative Prompt for Professional Images:**
```
poorly drawn, bad anatomy, wrong proportions, extra limbs,
cloned face, disfigured, gross proportions, malformed limbs,
missing arms, missing legs, extra arms, extra legs, fused fingers,
too many fingers, long neck, blurry, low quality, watermark,
text, signature
```

**For Realistic Faces:**
```
poorly drawn face, bad face, fused face, ugly face, worst face,
asymmetrical, unrealistic skin texture, double face
```

**For Hands:**
```
extra digits, extra hands, fused fingers, malformed limbs,
mutated hands, poorly drawn hands, extra fingers, missing hands,
bad hands, three hands, too many fingers, deformed hands
```

## Advanced Features

### ControlNet (Stable Diffusion models only)

Control composition and poses with reference images:

| Type | Use Case |
|------|----------|
| **Canny** | Match edge composition from reference |
| **Open Pose** | Control body position/orientation |
| **Super Clone** | Replicate specific person from photo |
| **Composition** | Arrange objects like reference |

### LoRA Models

Fine-tune specific attributes:

- **Hair LoRA**: -8 (straight) to 8 (maximum curl)
- **Logo Design LoRA**: Specific brand aesthetics
- **Custom LoRAs**: Per-model availability

### Seed Numbers

- Use same seed to replicate exact image
- Find seeds in Gallery for images you like
- Useful for creating consistent character across images

## E-Learning Use Cases

### Course Thumbnails
```
Prompt: excited young professional celebrating at computer,
confetti, bright modern office, success moment, 16:9,
photorealistic, high energy
Model: Flux or Epic Realism
Aspect: 16:9
```

### Module Header Images
```
Prompt: professional workspace with laptop and coffee,
resume on screen, morning light, shallow depth of field,
photorealistic
Model: Crystal Clear XL
Aspect: 16:9 or 3:1 for banners
```

### Concept Illustrations
```
Prompt: flat design illustration of networking concept,
people connected by lines, diverse professionals,
modern style, blue and orange color scheme
Model: Vector or illustration model
```

### Profile/Avatar Images
```
Prompt: professional headshot, diverse business person,
friendly expression, neutral background, studio lighting
Model: Super Portrait
Aspect: 1:1
```

### Infographic Backgrounds
```
Prompt: abstract minimal gradient, soft professional colors,
clean, suitable for text overlay, corporate style
Model: Any SDXL model
```

## Post-Processing Tools

After generating, use built-in tools:

| Tool | Purpose |
|------|---------|
| **Remove BG** | Transparent background for overlays |
| **Upscale 4X** | Increase resolution with detail enhancement |
| **Face Swap** | Replace faces (use ethically) |
| **Image-to-Prompt** | Analyze existing image for prompt ideas |

## Workflow for Course Development

### Batch Generation Strategy

1. **Create Prompt Templates** for each image type needed
2. **Generate Variations** - Create 3-4 options per concept
3. **Note Successful Seeds** - Reuse for consistency
4. **Upscale Final Selections** - Use 4X upscale for print/high-res

### Maintaining Visual Consistency

- Use same model for similar image types
- Keep consistent prompt style elements
- Note and reuse seeds for character consistency
- Maintain color scheme keywords across prompts

### Credit Management

| Plan | Credits | Best For |
|------|---------|----------|
| Starter ($5) | 100 | Testing |
| Apprentice ($19/mo) | 1,000 | Light use |
| Master ($49/mo) | 4,000 | Heavy development |

## Prompt Library for Job Search Course

### Resume & Documents
```
professional resume document on desk, pen beside it,
soft natural lighting, shallow depth of field, clean
modern aesthetic, photorealistic
```

### Interview Scenes
```
confident job candidate in interview, positive body
language, professional attire, modern conference room,
natural window lighting, photorealistic
```

### Networking
```
professionals networking at event, diverse group,
friendly conversation, modern venue, warm lighting,
photorealistic
```

### Job Search
```
person using laptop for job search, focused expression,
coffee shop or home office, natural lighting,
contemporary setting, photorealistic
```

### Success/Celebration
```
young professional receiving job offer, excited expression,
holding offer letter, bright natural light, joyful moment,
photorealistic
```

### LinkedIn Profile
```
professional headshot suitable for LinkedIn, confident
smile, business casual, neutral background, soft studio
lighting, photorealistic, approachable
```

## Quality Checklist

Before using generated images:

- [ ] Image matches intended purpose and mood
- [ ] No visible AI artifacts (extra fingers, distorted faces)
- [ ] Appropriate for target audience (diverse, relatable)
- [ ] Resolution sufficient for delivery format
- [ ] Consistent with course visual style
- [ ] Background suitable for text overlay (if needed)
- [ ] Proper licensing for commercial e-learning use

## Tips for Best Results

1. **Be Specific**: More detail = better results
2. **Use Quality Modifiers**: "high quality, professional, 8k, detailed"
3. **Specify Lighting**: "natural light, studio lighting, soft lighting"
4. **Include Mood**: "confident, friendly, professional, energetic"
5. **Iterate**: Generate multiple versions, refine prompts
6. **Check Gallery**: Find inspiration and working prompts
7. **Use Prompt Book**: Pre-made prompts for quick starts
8. **Match Model to Task**: Photorealistic vs illustration needs different models

## Resources

- **Supermachine Gallery**: See what prompts create which results
- **Prompt Book**: Pre-made prompt templates within platform
- **Style Assistance**: Built-in tools for emotions, lighting, camera settings
- **Model Tags**: Use "Photorealistic" tag for realistic needs

## Troubleshooting

### Poor Image Quality
- Increase steps (if available)
- Use higher resolution
- Add quality modifiers to prompt
- Try different model

### Faces Look Wrong
- Add negative prompt for face issues
- Use portrait-specific model
- Reduce prompt guidance slightly

### Hands Look Distorted
- Add hand-specific negative prompt
- Crop or position hands out of frame
- Use post-editing if needed

### Text Not Rendering
- Use Flux model (only one with text capability)
- Keep text short and simple
- Specify text clearly in quotes

### Inconsistent Style
- Use same model throughout project
- Create and reuse prompt templates
- Save and reuse successful seeds

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/webmasterarbez) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
