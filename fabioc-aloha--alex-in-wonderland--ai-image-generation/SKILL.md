---
name: ai-image-generation
description: AI visual content generation for books and presentations using FLUX and Ideogram models Use when this capability is needed.
metadata:
  author: fabioc-aloha
---

# AI Image Generation

**Domain**: Image generation, illustration creation, visual content for books and presentations

**When to use**: Creating cover art, chapter illustrations, character portraits, scene visualizations, or any visual content from text descriptions

**Models available**: FLUX Schnell (fast/cheap), FLUX Pro (high quality), Ideogram (best text), SDXL (customizable)

---

## Quick Reference

**Best for speed**: FLUX Schnell (~$0.003, 2-5 sec)  
**Best for quality**: FLUX Pro ($0.04, 5-20 sec)  
**Best for text**: Ideogram ($0.08, 15-25 sec)  
**Most control**: Stable Diffusion XL (variable)

**First step**: Discover user's aesthetic preference (realistic vs line art) before mass generation  
**Key insight**: Cost ≠ Quality always — FLUX Schnell can be production-worthy for simple compositions

---

## Skill Levels

### Level 1: Basic Usage

Generate images using pre-configured prompts and default settings.

**Use when**:
- Creating standard book illustrations
- Need quick visualization of a scene
- Working with established style guide

**Commands**:
```bash
# Generate cover
npm run generate:cover

# Generate chapter illustrations
npm run generate:illustrations

# Single illustration
node scripts/generate-single.js mid
```

**MCP Usage**:
```
@replicate run black-forest-labs/flux-schnell with prompt "boy with magnifying glass"
```

---

### Level 2: Advanced Generation

Create variations, compare models, test character consistency, and customize prompts.

**Use when**:
- Need multiple options to choose from
- Comparing quality across models
- Testing character consistency for multi-scene projects
- Fine-tuning prompt for specific results

**Commands**:
```bash
# Generate 3 variations
node scripts/generate-variations.js --type=cover --model=flux-pro --count=3

# Test character consistency (6 scenes × 2 styles)
node scripts/generate-consistency-test.js

# Compare models
node scripts/generate-cover.js --model=flux-schnell
node scripts/generate-cover.js --model=flux-pro
node scripts/generate-cover.js --model=ideogram

# Custom prompt
node scripts/generate-single.js custom --prompt="your prompt here"
```

**MCP Usage**:
```
@replicate search "flux image generation"
@replicate run black-forest-labs/flux-1.1-pro with prompt "..."
```

---

### Level 3: Expert Workflows

Fine-tune custom models, batch process, and integrate into publishing pipeline.

**Use when**:
- Building consistent visual brand
- Processing entire book at once
- Creating custom fine-tuned models

**Capabilities**:
- Fine-tune FLUX LoRA on your art style
- Batch process all chapters
- Upscale with super-resolution models
- Generate video from illustrations
- Custom deployment for production

**Resources**: See [resources/](resources/) for:
- Fine-tuning guides
- Batch processing templates
- Model comparison matrices
- Cost optimization strategies

---

## Models Reference

| Model | Cost | Speed | Quality | Best For |
|-------|------|-------|---------|----------|
| **FLUX Schnell** | $0.003 | 2-5s | Good* | Testing, simple compositions, expressions |
| **FLUX Dev** | $0.025 | 5-15s | Very Good | Development |
| **FLUX Pro** | $0.04 | 5-20s | Excellent | Atmospheric scenes, complex backgrounds |
| **Ideogram v3** | $0.08 | 15-25s | Excellent | Text rendering |
| **Recraft v3** | $0.04 | 10-20s | Excellent | Design-focused |
| **SDXL** | Variable | Variable | Good | Custom fine-tunes |

*Validated finding: FLUX Schnell can produce production-quality work for simple compositions (portraits, clear expressions)

---

## Style Guidelines

**CRITICAL**: Discover user's aesthetic preference first:
- Don't assume B&W line art for middle-grade books
- Generate 2-3 style samples (realistic vs line art)
- Let user explicitly choose preferred aesthetic
- Adjust model strategy accordingly

**For realistic/photorealistic style**:
```
photorealistic 3D render, atmospheric cinematic lighting,
professional middle-grade book illustration,
vibrant color palette, dramatic composition
```

**For B&W line art style**:
```
black and white line art, middle-grade book illustration, 
clean lines, no shading, suitable for ages 8-12
```

**For covers** (add to style above):
```
professional book cover design, vertical orientation (2:3), 
dramatic composition, sense of mystery and intelligence
```

---

## Common Patterns

**Discover style preference** (do this first!):
```bash
# Generate realistic sample
node scripts/generate-cover.js --model=flux-pro
# Generate line art sample  
node scripts/generate-single.js opening --model=flux-schnell
# User chooses → document preference
```

**Test character consistency** (for multi-scene books):
```bash
node scripts/generate-consistency-test.js
# Review outputs → Rate consistency 1-5
# 4-5 stars = viable for book illustration
```

**Generate cover variations**:
```bash
node scripts/generate-variations.js --type=cover --count=5
```

**Generate all chapter 1 scenes**:
```bash
node scripts/generate-illustrations.js 1
```

**Quick test** (may be production-worthy!):
```bash
node scripts/generate-single.js opening --model=flux-schnell
```

**Atmospheric/complex scenes**:
```bash
node scripts/generate-variations.js --model=flux-pro --count=3
```

---

## Troubleshooting

**Rate limiting (429 error)**:
- Script auto-waits and retries
- With <$5 credit: 6 requests/minute
- Add more credit for higher limits

**No output received**:
- FLUX Pro may return different format
- Script handles this automatically
- Check `assets/variations/` for output

**Insufficient credit (402 error)**:
- Visit https://replicate.com/account/billing
- Add $10+ credit (gets ~250 FLUX Pro images)
- Wait 2-3 minutes for activation

---

## Cost Optimization

**For testing**: Use FLUX Schnell ($0.003)
**For review**: Generate 3-5 variations, pick best
**For production**: Use FLUX Pro only on final selected prompts
**For covers with text**: Try Ideogram if text is critical

**Estimated costs**:
- Full book (30 chapters × 3 illustrations): $0.27 (Schnell) or $3.60 (Pro)
- Cover variations (5 options): $0.20 (Pro)
- Character portraits (10 variations): $0.40 (Pro)

---

## Related Skills

- **markdown-mermaid**: Diagrams and flowcharts
- **svg-graphics**: Vector illustrations (free, full control)
- **brand-asset-management**: Logo and visual identity

---

## Next Steps

1. Generate first image: `npm run generate:cover`
2. Review output in `assets/`
3. Try variations to compare: `node scripts/generate-variations.js --count=3`
4. Choose best and iterate on prompt
5. Scale to full book production

**Documentation**: [docs/IMAGE-GENERATION-SETUP.md](../../docs/IMAGE-GENERATION-SETUP.md)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/fabioc-aloha) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
