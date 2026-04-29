---
name: readme-banner-generation
description: Ultra-wide cinematic banner generation for GitHub README headers using Flux models Use when this capability is needed.
metadata:
  author: fabioc-aloha
---

# Domain Knowledge: AI-Generated README Banner Creation

**Domain**: Project documentation, visual branding, AI image generation
**Context**: Creating professional cinematic banners for GitHub README headers using Flux image models
**Learned**: February 15, 2026
**Project**: Alex in Wonderland (book project)

---

## Core Pattern

**Problem**: ASCII art banners are limited and don't leverage existing AI image generation infrastructure.

**Solution**: Generate ultra-wide cinematic banner images using Flux models with project-specific aesthetic blending.

---

## Implementation Details

### 1. Aspect Ratio Selection

**Flux-supported ratios** (as of 2026):
- ✅ `21:9` - Ultra-wide cinematic (best for README banners)
- ✅ `16:9` - Standard widescreen
- ❌ `16:3` - NOT supported (causes 422 validation error)

**Recommendation**: Use `21:9` for maximum banner width while staying within API constraints.

### 2. Banner Prompt Engineering

**Compositional Structure** for ultra-wide format:
```
- Left side: Main character/protagonist (1/3)
- Center: Thematic element/magical focal point (1/3)
- Right side: Environmental/world context (1/3)
```

**Critical Elements**:
- Specify ultra-wide format explicitly in prompt
- Include lighting directions (noir three-point, rim light, etc.)
- Balance photorealistic character rendering with stylized environments
- Reserve compositional breathing room for text overlays (if needed)

**Example Prompt Structure**:
```
Cinematic ultra-wide banner illustration for "[PROJECT NAME]" book header.
[Genre/aesthetic description]

COMPOSITION (21:9 ultra-wide cinematic format):
- Left side: [Character description with specific details]
- Center: [Thematic focal point, magical elements]
- Right side: [Environment, setting, atmosphere]

LIGHTING:
- [Specific lighting techniques for each compositional zone]

STYLE:
- [Art direction, realism level, proportions]

COLOR PALETTE:
- [Dominant colors, accents, contrast notes]

MOOD: [Emotional tone]
QUALITY: [Output technical requirements]
```

### 3. Model Selection for Banners

| Model | Cost | Best For | Aspect Ratios | Notes |
|-------|------|----------|---------------|--------|
| **Flux Schnell** | $0.003 | Testing, iteration | 21:9, 16:9, 9:16, etc. | Fast generation, good quality for previews |
| **Flux 1.1 Pro** | $0.04 | Production (no text) | 21:9, 16:9, 9:16, etc. | Higher detail, better composition |
| **Ideogram v2** | $0.08 | Text overlays | 3:1 max (widest), 16:9, etc. | **Best for typography**, crystal clear text |

**Workflow**:
- **Clean banners**: Test with Schnell → Refine prompt → Generate final with Pro
- **Typography banners**: Start with Ideogram (text quality critical)

**Critical Decision**: With or without typography?
- **Without text**: Use Flux for flexibility, lower cost, easier iteration
- **With text**: Use Ideogram for superior text rendering quality

---

### 4. Typography on Banners (Ideogram)

**When to add text to banners**:
- ✅ Project title needs immediate visibility
- ✅ Brand recognition through consistent typography
- ✅ Standalone banner for social sharing (no context needed)
- ❌ Text changes frequently (use markdown instead)
- ❌ Multi-language support needed (separate text layer better)

#### Ideogram-Specific Parameters

**Aspect Ratio Constraints**:
- Ideogram supports: `3:1` (widest), `16:9`, `4:3`, `3:2`, `2:3`, `16:10`, `10:16`, `1:3`, etc.
- Does NOT support: `21:9` (Flux's ultra-wide)
- **Best for banners**: `3:1` ratio at `1536x512` resolution (highest quality wide format)

**Required Parameters** (case-sensitive!):
```javascript
const input = {
  prompt: BANNER_PROMPT,
  aspect_ratio: '3:1',                  // Note: NOT '21:9'
  magic_prompt_option: 'On',            // MUST be 'On', 'Off', or 'Auto' (capitalized)
  style_type: 'Realistic',              // Options: 'Realistic', 'General', 'Design', 'Render 3D', 'Anime', 'None', 'Auto'
  resolution: '1536x512',               // Specific resolution (not '1440p')
  output_format: 'png',
};
```

**Common Mistakes**:
- ❌ `magic_prompt_option: 'ON'` → Must be `'On'` (case-sensitive)
- ❌ `style_type: 'CINEMATIC'` → Not a valid option, use `'Realistic'`
- ❌ `resolution: '1440p'` → Must use exact dimensions like `'1536x512'`
- ❌ `aspect_ratio: '21:9'` → Ideogram doesn't support this, use `'3:1'`

#### Ideogram URL Handling Quirk

**Problem**: Ideogram returns URL as a getter function, not a string.

```javascript
const output = await replicate.run('ideogram-ai/ideogram-v2', { input });

// ❌ This will fail:
const imageUrl = output[0];  // Returns [Function: url]

// ✅ Correct handling:
let imageUrl;
if (Array.isArray(output)) {
  imageUrl = output[0];
} else if (typeof output === 'string') {
  imageUrl = output;
} else if (output && typeof output.url === 'function') {
  // Ideogram-specific: URL is a getter function
  imageUrl = output.url().toString();
} else if (output && output.url) {
  imageUrl = output.url;
}

// Additional safety: Ensure it's a string
if (typeof imageUrl === 'object' && imageUrl.href) {
  imageUrl = imageUrl.href;
}
```

#### Typography Prompt Engineering

**Structure for text clarity**:

```
TITLE TEXT (centered, top third):
"[PROJECT NAME]"
- Large bold serif font (elegant, mysterious)
- Color: [Gradient or solid with glow]
- Sharp, perfectly legible lettering
- Positioned in [location] with dramatic presence

SUBTITLE TEXT (below title):
"[Tagline]"
- Smaller elegant serif font
- Color: [Complementary to title]
- Clean, readable typography

BACKGROUND COMPOSITION (3:1 ultra-wide format):
[Same compositional structure as clean banners]

LIGHTING & ATMOSPHERE:
- Text should stand out clearly against background
- Ensure text has subtle shadow/glow for perfect legibility
- Background should complement, not compete with text

TEXT QUALITY CRITICAL:
- Crystal clear, sharp letterforms
- No distortion, warping, or illegibility
- Perfect spelling: "[EXACT TEXT]"
- Professional typographic hierarchy
- Text placement allows character and environment to remain visible
```

**Key Principles**:
1. **Explicit spelling**: State exact text in quotes to avoid AI interpretation
2. **Legibility requirements**: Use words like "crystal clear", "sharp", "perfectly legible"
3. **Background contrast**: Specify shadows, glows, or contrast to ensure readability
4. **Typography hierarchy**: Define primary (title) vs secondary (subtitle) styling
5. **Integration**: Text should feel part of the composition, not pasted on

#### Cost Comparison: Text vs Clean

**With Typography** (Ideogram):
- Single generation: $0.08
- Iteration needed if text unclear: +$0.08 per attempt
- **Total**: $0.08-$0.24 (depends on text quality first try)

**Without Typography** (Flux + Markdown):
- Banner: $0.003-$0.04
- Text: Free (markdown overlays in README)
- **Total**: $0.003-$0.04
- **Flexibility**: Can change text anytime without regeneration

**Recommendation**:
- **Fixed branding**: Use Ideogram with text ($0.08, professional, shareable)
- **Iterative projects**: Use Flux clean + markdown text ($0.003-$0.04, flexible)

---

### 5. Script Templates

#### Clean Banner (Flux)

**Node.js script pattern**:
```javascript
const input = {
  prompt: BANNER_PROMPT,
  aspect_ratio: '21:9',  // Ultra-wide
  output_format: 'png',
  output_quality: 100,
};

const output = await replicate.run('black-forest-labs/flux-schnell', { input });
const imageUrl = Array.isArray(output) ? output[0] : output;
// Download and save to assets/banner-{model}.png
```

#### Typography Banner (Ideogram)

**Node.js script pattern**:
```javascript
const input = {
  prompt: BANNER_PROMPT_WITH_TEXT,
  aspect_ratio: '3:1',              // Ideogram's widest
  magic_prompt_option: 'On',        // Enhance text rendering
  style_type: 'Realistic',          // Photorealistic style
  resolution: '1536x512',           // Highest quality for 3:1
  output_format: 'png',
};

const output = await replicate.run('ideogram-ai/ideogram-v2', { input });

// Handle Ideogram's URL getter function
let imageUrl;
if (output && typeof output.url === 'function') {
  imageUrl = output.url().toString();
} else if (typeof imageUrl === 'object' && imageUrl.href) {
  imageUrl = imageUrl.href;
}
// Download and save to assets/banner-with-text-{model}.png
```

**Package.json integration**:
```json
"scripts": {
  "generate:banner": "node scripts/generate-banner.js",
  "generate:banner-text": "node scripts/generate-banner-with-text.js"
}
```

### 6. README Integration

#### With Typography (Text Embedded)

```markdown
<div align="center">

![Project Name](assets/banner-with-text-ideogram.png)

**[Project tagline]**

*[Subtitle or origin story]*

</div>

> **Banner Options**:
> • With typography: `assets/banner-with-text-ideogram.png` (current, $0.08)
> • Without text: `assets/banner-flux-schnell.png` (cleaner, $0.003)
> • Generate new: `npm run generate:banner-text` or `npm run generate:banner`
```

#### Without Typography (Clean Image)

```markdown
<div align="center">

![Project Name](assets/banner-flux-schnell.png)

**[Project tagline]**

*[Subtitle or origin story]*

</div>

> **Note**: Generate banner with: `npm run generate:banner` (or `--model=flux-pro` for production quality)
```

**Trade-offs**:
- **Typography embedded**: Professional, shareable, consistent branding, higher cost ($0.08)
- **Clean image + markdown text**: Flexible, cheaper ($0.003), easy to update text, less polished for sharing

---

## Key Insights

### Aesthetic Blending for Genre Fusion

**Challenge**: Combining multiple visual genres (noir detective + fantasy wonderland)

**Technique**:
- Separate compositional zones by genre
- Use lighting to unify disparate elements (noir on character, magical on center, atmospheric on environment)
- Consistent color palette bridges styles (purples/teals + amber/charcoal)

### Prompt Specificity vs. Model Freedom

**Too vague**: "Create a mysterious book banner"
→ Model makes random choices, inconsistent with brand

**Too specific**: "Exactly 1200x300px with Alex at coordinates X,Y"
→ Model struggles with over-constraint

**Balanced**: Compositional zones + lighting + mood + color palette
→ Model has creative freedom within brand guidelines

### API Error Handling

**Common failure**: Unsupported aspect ratio
- Error: `422 Unprocessable Entity: aspect_ratio must be one of...`
- Solution: Reference current API documentation for supported ratios
- Fallback: Use `21:9` or `16:9` as safe defaults

---

## Reusability Guidelines

### When to Use This Pattern

✅ **Good fit**:
- GitHub README headers
- Project landing pages
- Documentation portals
- Marketing materials with wide format

❌ **Not ideal for**:
- Social media posts (need 1:1, 4:5, 16:9)
- Book covers (need portrait 2:3, 4:5)
- Character reference sheets (need square 1:1)

### Adaptation for Other Projects

**Required changes**:
1. Update `BANNER_PROMPT` with new character/setting/genre
2. Adjust color palette to match brand
3. Modify compositional zones based on story elements

**Reusable elements**:
- Script structure (download, save, CLI args)
- npm script pattern
- README integration template
- Model selection workflow (test → refine → produce)

### Cross-Project Patterns

**Shared with character reference generation**:
- Flux model progression (Schnell → Pro)
- Replicate API error handling
- Prompt engineering structure (character → lighting → style → mood)

**Unique to banners**:
- Ultra-wide compositional thinking (left-center-right zones)
- Text overlay space reservation
- Genre blending in single horizontal composition

---

## Cost Optimization

**Full banner workflow**:
- Testing: 2-3 Schnell generations = $0.006-$0.009
- Final: 1 Flux Pro generation = $0.04
- **Total**: ~$0.05 per banner

**Comparison to alternatives**:
- Midjourney: $10-30/month subscription (overkill for single banner)
- Custom illustration: $50-500 per piece
- Photo stock: $10-100 per image (licensing restrictions)

**ROI**: AI generation is 100-1000x cheaper for project branding needs.

---

## Future Enhancements

**Potential additions**:
- Animated variants (Stable Video Diffusion for subtle particle motion)
- Seasonal themes (generate variants for holidays/milestones)
- Multi-language text overlays (generate multiple Ideogram versions per language)
- Accessibility: Generate alt text descriptions using vision models
- Text outline/stroke: Improve legibility on complex backgrounds
- Dynamic text: Template system for easy project name substitution

**Tool integration**:
- MCP server command: `@replicate generate-banner --project="Alex in Wonderland" --with-text`
- VS Code extension: Right-click README → "Generate Banner" (with typography toggle)
- GitHub Action: Auto-generate banner on project creation from template

---

## Tags

`ai-image-generation`, `flux`, `ideogram`, `replicate`, `readme-enhancement`, `visual-branding`, `project-documentation`, `prompt-engineering`, `banner-design`, `ultra-wide`, `compositional-layout`, `typography`, `text-rendering`, `ai-typography`

---

## Citations

- Replicate Flux API: https://replicate.com/black-forest-labs/flux-schnell
- Replicate Ideogram API: https://replicate.com/ideogram-ai/ideogram-v2
- Aspect ratio validation: Replicate API error response (422 status)
- Ideogram parameter validation: Trial-and-error with API errors (magic_prompt_option, style_type case sensitivity)
- Ideogram URL handling: Direct experience with getter function return type
- Character rendering consistency: Alex in Wonderland noir-style-refined.md prompts
- Cost estimates: Replicate pricing page (Feb 2026)
- Typography quality: Ideogram v2 tested superior to Flux for text rendering (Feb 2026)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/fabioc-aloha) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
