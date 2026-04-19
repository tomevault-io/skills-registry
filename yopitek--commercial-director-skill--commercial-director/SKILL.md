---
name: commercial-director
description: AI-powered commercial video generation with multi-agent workflow for creating storyboards, image prompts, and video animation prompts Use when this capability is needed.
metadata:
  author: yopitek
---

# Commercial Director Skill

Generate professional commercial materials using a coordinated multi-agent workflow.

## Capabilities

This skill enables Claude to:
1. **Analyze products** - Extract specifications from images and documentation
2. **Create storyboards** - Generate scene-by-scene commercial scripts
3. **Design visuals** - Create AI image generation prompts
4. **Animate scenes** - Generate video animation prompts for Google Veo

## Workflow

```
Product Assets → [Product Analyst] → Specs
                        ↓
                [Storyboard Writer] → Script
                        ↓
                [Visual Designer] → Image Prompts
                        ↓
                [Video Prompter] → Video Prompts
```

## Usage

### Command-Line
```bash
npm run generate -- --name "Product Name" --path "./assets" --style cyberpunk
```

### Programmatic
```typescript
import { generateCommercial } from 'commercial-video-sdk';

await generateCommercial({
  productName: 'ALFA AWUS036ACH',
  assetPath: './036ach_asset',
  duration: 30,
  style: 'cyberpunk'
});
```

## Available Styles (Robot/Sci-Fi Focused)

| Style | Chinese | Mood | Reference |
|-------|---------|------|-----------|
| `cyberpunk` | 駭客科技風 | Dark, mysterious | The Matrix |
| `transformers-battle` | 變形金剛戰鬥風 | Intense, powerful | Transformers |
| `bumblebee` | 大黃蜂風格 | Friendly, heroic | Bumblebee (2018) |
| `neon-city` | 霓虹都市風 | Atmospheric, noir | Blade Runner |
| `space-opera` | 太空歌劇風 | Epic, majestic | Star Wars |

## Output Files

Generated in `{assetPath}/script/`:
- `product_specs.md` - Detailed product specifications
- `storyboard_{style}.md` - Scene-by-scene storyboard
- `image_prompts_{style}.md` - Image generation prompts
- `video_prompts_{style}.md` - Video animation prompts

## Asset Requirements

Place in your asset folder:
- Product images (`.png`, `.jpg`)
- Character/robot references (if applicable)
- `req.md` - Product requirements (optional)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/yopitek) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
