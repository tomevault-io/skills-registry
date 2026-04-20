---
name: brand-consistent-visuals
description: Generate professional brand visualizations that maintain consistent brand identity across all creative assets using multi-step AI analysis and brand guidelines. Use when this capability is needed.
metadata:
  author: tara-shopos
---

# Brand-Consistent Visuals

Generate professional brand visualizations that maintain consistent brand identity across all creative assets. This skill uses multi-step AI analysis to understand brand positioning, create strategic creative briefs, and generate visuals that authentically represent the brand's identity, values, and visual language.

## Overview

This skill enables AI agents to create brand-consistent visual content by:
- Analyzing comprehensive brand inputs (website, logos, colors, fonts, guidelines)
- Understanding brand positioning, personality, and target audience
- Creating or enhancing creative briefs with professional creative direction
- Generating diverse, high-quality brand visualizations that maintain visual consistency
- Ensuring all outputs align with brand identity and strategic positioning

## When to Use This Skill

Use this skill when you need to:
- Generate brand visualizations that maintain consistent identity
- Create marketing materials aligned with brand guidelines
- Develop creative concepts that reflect brand positioning
- Produce diverse brand assets (social media, ads, mockups, editorial)
- Ensure visual consistency across multiple creative outputs
- Translate brand strategy into visual executions
- Create professional brand presentations and mood boards

## Prerequisites

**Required Inputs:**
- `website_url` (string, required): Brand's website URL - primary source for brand analysis
- Image generation capability (Gemini 3 Pro or equivalent)

**Optional Inputs:**
- `custom_description` (string): Creative brief describing what to create
- `brand_logo` (string): Single brand logo URL or base64 data URI
- `brand_logos` (array): Multiple brand logo URLs or data URIs
- `brand_info` (string): Brand information (colors, fonts, guidelines)
- `brand_memory` (object): Structured brand data with name, overview, palette, fonts
- `num_variations` (integer): Number of variations to generate (1-12, default: 3)
- `aspect_ratio` (string): Aspect ratio for outputs (default: "1:1")
- `output_format` (string): Output format - png, jpg, webp (default: "png")

## Step-by-Step Instructions

### Step 1: Comprehensive Brand Analysis

Analyze all brand inputs to understand positioning and create strategic direction:

**Inputs to Analyze:**
- Website URL (primary source of truth)
- Brand logos and visual assets
- Brand information (colors, fonts, guidelines)
- Brand memory data (if available)
- Custom creative description (if provided)

**Analysis Tasks:**
1. **Identify Brand Industry**: Determine the brand's primary industry category based on positioning, not surface-level products
2. **Extract Brand Intelligence**:
   - Brand maturity (startup, growth-stage, established, premium)
   - Target audience sophistication
   - Brand personality (bold, minimal, playful, serious, luxury, utilitarian)
   - Visual culture (modern, timeless, experimental, conservative)
3. **Create/Enhance Creative Brief**:
   - If no custom description: Create comprehensive creative brief from scratch
   - If custom description provided: Elevate it with clearer creative direction
   - Define WHAT is being designed (brand kit, ads, social, packaging)
   - Explain WHY (brand goal, positioning, audience)
   - Define HOW it should look and feel (style, tone, visual language)
   - Reference brand colors, typography, and design principles

**Output:**
```typescript
{
  brand_industry: string;           // e.g., "Fashion & Apparel"
  enhanced_creative_brief: string;  // Detailed professional creative brief
  brand_insights: string;           // 2-3 sharp sentences on positioning
}
```

### Step 2: Categorize Creative Request

Identify the full range of creative assets required to prevent collapsing into a single format:

**Classification Rules:**
- Identify PRIMARY assets (main deliverables)
- Identify SECONDARY assets (supporting formats)
- Define USAGE CONTEXT (where/how these will be used)
- Think about complete brand identity system

**Output:**
```typescript
{
  primary_assets: string[];    // e.g., ["brand mockups", "logo applications"]
  secondary_assets: string[];  // e.g., ["social", "web", "editorial"]
  usage_context: string;       // e.g., "brand identity system"
}
```

### Step 3: Generate Professional Image Prompts

Create detailed, unique prompts for each brand visualization:

**Prompt Requirements:**
1. **Detailed Descriptions (150-300 words each)**:
   - Specific visual details: textures, materials, finishes, surfaces
   - Lighting details: direction, quality, color temperature, shadows
   - Camera details: angle, distance, focal length, depth of field
   - Composition: framing, negative space, visual hierarchy
   - Colors: specific hues, saturation, contrast relationships
   - Mood and atmosphere: energy, emotion, brand personality

2. **Uniqueness Requirements (CRITICAL)**:
   - Each prompt must be COMPLETELY UNIQUE
   - NO repeating backgrounds across prompts
   - NO repeating copy/descriptions across prompts
   - Each background must be DISTINCT (no concrete walls/floors)
   - Each prompt uses different descriptive language
   - NO sequential or hierarchical relationships between prompts

3. **Professional Standards**:
   - Authentic photography with realistic physics
   - Proper model physics (if models included): realistic poses, proper grounding
   - Text/design placement logic: proper alignment, spacing, integration
   - NO hallucinations: NO floating objects, NO impossible physics
   - Material realism: accurate shadows, depth, scale, perspective
   - Design restraint: clean compositions, intentional use of space

4. **Aspect Ratio Specification**:
   - Each prompt must end with `--ar X:X` format
   - Choose appropriate ratio for creative type:
     - Product close-ups, square social: `--ar 1:1`
     - Portrait/vertical: `--ar 9:16` or `--ar 4:5`
     - Landscape/wide: `--ar 16:9` or `--ar 21:9`
     - Standard prints: `--ar 4:3` or `--ar 3:4`
     - Posters: `--ar 3:2` or `--ar 2:3`

**Output:**
```typescript
{
  prompts: string[];  // Array of detailed prompts, each ending with --ar X:X
}
```

### Step 4: Generate Brand Visualizations

Generate images using the detailed prompts and brand assets:

**Generation Parameters:**
- Use Gemini 3 Pro Image Preview or equivalent
- Include brand logos as reference images (converted to S3 URLs)
- Extract aspect ratio from each prompt (if using "default" setting)
- Apply validated output format
- Generate all variations concurrently for efficiency

**Quality Checks:**
- Verify each generated image has a valid URL
- Check for generation errors in metadata
- Filter out failed generations
- Ensure brand consistency across all outputs

**Output:**
```typescript
{
  success: boolean;
  outputAssets: Array<{
    type: "image";
    url: string;
    metadata: {
      prompt: string;
      aspect_ratio: string;
      model_used: string;
      generation_time: number;
    };
  }>;
}
```

## Examples

### Example 1: Fashion Brand Social Media Assets

**Input:**
```typescript
{
  website_url: "https://urbanvault.fashion",
  custom_description: "Create social media content showcasing our minimalist aesthetic",
  brand_logos: ["https://example.com/logo.png"],
  brand_info: "name: Urban Vault, palette: { primary: [{ hex: #000000, label: black }], secondary: [{ hex: #FFFFFF, label: white }] }",
  num_variations: 4,
  aspect_ratio: "1:1",
  output_format: "png"
}
```

**Step 1 Output (Brand Analysis):**
```typescript
{
  brand_industry: "Fashion & Apparel - Contemporary Minimalist",
  enhanced_creative_brief: "Create a series of social media visuals for Urban Vault, a contemporary minimalist fashion brand targeting sophisticated urban professionals aged 25-40. The brand embodies clean lines, monochromatic palettes, and architectural silhouettes. Visual direction: High-contrast black and white photography with strong geometric compositions. Each image should feature the brand's signature minimalist aesthetic through carefully curated lifestyle moments - morning coffee rituals, urban commutes, workspace details. Emphasize negative space, clean typography, and subtle brand integration. The mood is calm, intentional, and refined. Avoid busy backgrounds or excessive styling. Each visual should feel like a moment of clarity in urban chaos.",
  brand_insights: "Urban Vault positions itself as the antidote to fast fashion chaos, offering timeless minimalist pieces for the intentional consumer. Visual identity emphasizes restraint, quality, and architectural precision."
}
```

**Step 2 Output (Asset Categorization):**
```typescript
{
  primary_assets: ["social media posts", "Instagram grid content"],
  secondary_assets: ["stories", "carousel posts", "brand mood boards"],
  usage_context: "social media brand presence and community engagement"
}
```

**Step 3 Output (Image Prompts - Sample 1 of 4):**
```
Professional lifestyle photography of a minimalist morning scene. A pristine white marble countertop occupies the lower third of the frame, featuring a single black ceramic coffee cup positioned using the rule of thirds. Steam rises delicately from the cup, backlit by soft morning light streaming through a nearby window. The Urban Vault logo appears subtly embossed on a small black business card leaning against the cup. Background: floor-to-ceiling windows revealing a blurred cityscape, creating depth through atmospheric perspective. The composition uses extreme negative space - 70% of the frame is clean white and soft gray tones. Lighting: natural diffused morning light from camera left, creating soft shadows that define the cup's form. Camera: 50mm lens at f/2.8, shallow depth of field keeping the cup sharp while the background gently blurs. Color palette: monochromatic with subtle warm undertones in the marble. Mood: serene, intentional, refined. The scene feels like a quiet moment of clarity before the day begins. Typography: "Start Intentionally" in thin sans-serif font, positioned in the upper right negative space, size 24pt, perfectly aligned with the grid. Material details: the marble shows subtle natural veining, the ceramic has a matte finish with soft light reflections, the business card has a slight texture. Spatial relationships: the cup sits 3 inches from the card, both properly grounded on the surface with realistic contact shadows. No floating elements, no impossible physics. --ar 1:1
```

**Step 4 Output (Generated Images):**
```typescript
{
  success: true,
  outputAssets: [
    {
      type: "image",
      url: "https://s3.amazonaws.com/shopos-assets/urban-vault-social-1.png",
      metadata: {
        prompt: "[Full prompt from Step 3]",
        aspect_ratio: "1:1",
        model_used: "gemini-3.0-pro-image-preview",
        generation_time: 12.4
      }
    },
    // ... 3 more variations with unique backgrounds and compositions
  ]
}
```

### Example 2: Tech Startup Brand Kit

**Input:**
```typescript
{
  website_url: "https://nexusai.tech",
  brand_memory: {
    name: "Nexus AI",
    overview: "AI-powered workflow automation for modern teams",
    tagline: { text: "Work smarter, not harder", tones: ["innovative", "approachable"] },
    palette: {
      primary: [{ hex: "#6366F1", label: "indigo" }],
      secondary: [{ hex: "#8B5CF6", label: "purple" }, { hex: "#EC4899", label: "pink" }]
    }
  },
  num_variations: 3,
  aspect_ratio: "16:9",
  output_format: "png"
}
```

**Step 1 Output (Brand Analysis):**
```typescript
{
  brand_industry: "Technology - SaaS & AI",
  enhanced_creative_brief: "Generate professional brand visualizations for Nexus AI, an innovative AI-powered workflow automation platform targeting modern tech-forward teams. The brand balances cutting-edge technology with human approachability. Visual direction: Create dynamic, futuristic compositions that showcase the brand's AI capabilities while maintaining warmth and accessibility. Use the brand's vibrant gradient palette (indigo to purple to pink) as a signature element. Each visual should demonstrate the concept of 'intelligent automation' through abstract representations of data flow, neural networks, or seamless integrations. Incorporate the tagline 'Work smarter, not harder' in modern sans-serif typography. Mood: innovative yet approachable, powerful yet friendly, technical yet human. Avoid cold corporate aesthetics or overly complex technical imagery.",
  brand_insights: "Nexus AI differentiates through approachable innovation - making powerful AI accessible to everyday teams. Visual identity combines technical sophistication with human warmth through vibrant gradients and clean design."
}
```

**Step 2 Output (Asset Categorization):**
```typescript
{
  primary_assets: ["brand visualizations", "product marketing assets"],
  secondary_assets: ["web banners", "presentation slides", "social media"],
  usage_context: "brand identity system and product marketing"
}
```

**Step 3 Output (Image Prompts - Sample 1 of 3):**
```
Abstract 3D visualization representing AI-powered workflow automation. The composition features a flowing network of luminous nodes and connections forming an organic neural pathway through three-dimensional space. Primary color: vibrant indigo (#6366F1) nodes pulsing with soft inner glow, connected by gradient lines transitioning from purple (#8B5CF6) to pink (#EC4899). The network flows from bottom-left to top-right, creating dynamic diagonal movement. Background: deep navy gradient fading to black, with subtle particle effects suggesting data streams. Lighting: each node emits soft volumetric light, creating atmospheric depth and dimension. The largest central node features the Nexus AI logo subtly integrated as a holographic projection. Camera: wide-angle perspective at 35mm equivalent, positioned slightly below the network looking up, creating an impressive sense of scale. Depth of field: foreground nodes sharp, background nodes softly blurred with bokeh effects. Typography: "Work smarter, not harder" positioned in the lower third, using modern geometric sans-serif (Outfit or similar), 48pt, white with 80% opacity, perfectly aligned with the grid. The text has a subtle glow effect matching the indigo brand color. Spatial composition: nodes distributed using the golden ratio, creating natural visual flow. Material properties: nodes have glass-like translucency with internal light diffusion, connections have fiber-optic luminescence. Physics: all elements properly positioned in 3D space with accurate perspective, realistic light falloff, and proper depth relationships. No floating elements without context. The overall effect is sophisticated, futuristic, and inviting - technical excellence made approachable. --ar 16:9
```

**Step 4 Output (Generated Images):**
```typescript
{
  success: true,
  outputAssets: [
    {
      type: "image",
      url: "https://s3.amazonaws.com/shopos-assets/nexus-ai-brand-1.png",
      metadata: {
        prompt: "[Full prompt from Step 3]",
        aspect_ratio: "16:9",
        model_used: "gemini-3.0-pro-image-preview",
        generation_time: 15.2
      }
    },
    // ... 2 more variations with unique compositions
  ]
}
```

### Example 3: Luxury Beauty Brand Editorial

**Input:**
```typescript
{
  website_url: "https://lumiere-beauty.com",
  custom_description: "Create editorial-style imagery for our new skincare line launch",
  brand_logos: ["data:image/png;base64,iVBORw0KGgoAAAANS..."],
  brand_info: "name: Lumière Beauty, palette: { primary: [{ hex: #D4AF37, label: gold }], secondary: [{ hex: #FFFFFF, label: white }, { hex: #F5F5DC, label: cream }] }, fonts: [{ family: Playfair Display, weight: 400 }]",
  num_variations: 4,
  aspect_ratio: "2:3",
  output_format: "jpg"
}
```

**Step 1 Output (Brand Analysis):**
```typescript
{
  brand_industry: "Beauty & Cosmetics - Luxury Skincare",
  enhanced_creative_brief: "Create sophisticated editorial imagery for Lumière Beauty's new skincare line, targeting affluent consumers aged 30-55 who value luxury, efficacy, and timeless elegance. The brand embodies French-inspired sophistication with a focus on radiant, luminous skin. Visual direction: Soft, ethereal photography with emphasis on natural light, delicate textures, and golden hour warmth. Each image should evoke the feeling of 'inner radiance' through careful use of the brand's signature gold accent against cream and white backgrounds. Feature the skincare products in elevated, art-directed still life compositions that feel more like fine art than traditional product photography. Incorporate natural elements (silk, marble, fresh botanicals) to suggest luxury and purity. Typography should use Playfair Display for elegance. Mood: luminous, sophisticated, timeless, aspirational. Avoid clinical or overly retouched aesthetics - embrace natural beauty and authentic luxury.",
  brand_insights: "Lumière Beauty positions itself as the intersection of French luxury and modern skincare science, promising transformative radiance through premium formulations. Visual identity emphasizes ethereal beauty, golden light, and timeless sophistication."
}
```

**Output continues with categorization, prompts, and generated images...**

## Integration Patterns

### TypeScript Backend Integration

#### Tool Definition for Claude

```typescript
import Anthropic from "@anthropic-ai/sdk";

const brandConsistentVisualsTool: Anthropic.Tool = {
  name: "generate_brand_consistent_visuals",
  description: "Generate professional brand visualizations that maintain consistent brand identity across all creative assets using multi-step AI analysis and brand guidelines. Analyzes brand positioning, creates strategic creative briefs, and generates visuals aligned with brand identity.",
  input_schema: {
    type: "object",
    properties: {
      website_url: {
        type: "string",
        description: "Brand's website URL (required) - primary source for brand analysis"
      },
      custom_description: {
        type: "string",
        description: "Creative brief describing what to create (optional). If not provided, the system will analyze the brand and generate suitable creative content."
      },
      brand_logo: {
        type: "string",
        description: "Single brand logo URL or base64 data URI (optional)"
      },
      brand_logos: {
        type: "array",
        items: { type: "string" },
        description: "Multiple brand logo URLs or data URIs (optional)"
      },
      brand_info: {
        type: "string",
        description: "Brand information including colors, fonts, guidelines (optional). Format: 'name: Brand Name, palette: { primary: [{ hex: #000000, label: black }] }'"
      },
      brand_memory: {
        type: "object",
        description: "Structured brand data with name, overview, palette, fonts, tagline (optional)",
        properties: {
          name: { type: "string" },
          overview: { type: "string" },
          tagline: {
            type: "object",
            properties: {
              text: { type: "string" },
              tones: { type: "array", items: { type: "string" } }
            }
          },
          palette: {
            type: "object",
            properties: {
              primary: {
                type: "array",
                items: {
                  type: "object",
                  properties: {
                    hex: { type: "string" },
                    label: { type: "string" }
                  }
                }
              },
              secondary: {
                type: "array",
                items: {
                  type: "object",
                  properties: {
                    hex: { type: "string" },
                    label: { type: "string" }
                  }
                }
              }
            }
          },
          fonts: {
            type: "array",
            items: {
              type: "object",
              properties: {
                family: { type: "string" },
                weight: { type: "string" },
                size: { type: "string" },
                color: { type: "string" }
              }
            }
          }
        }
      },
      num_variations: {
        type: "integer",
        description: "Number of variations to generate (1-12, default: 3)",
        minimum: 1,
        maximum: 12
      },
      aspect_ratio: {
        type: "string",
        description: "Aspect ratio for outputs. Options: '1:1', '2:3', '3:2', '16:9', '9:16', '4:3', '3:4', '4:5', '5:4', '21:9', 'default' (extracts from prompts)",
        enum: ["1:1", "2:3", "3:2", "16:9", "9:16", "4:3", "3:4", "4:5", "5:4", "21:9", "default"]
      },
      output_format: {
        type: "string",
        description: "Output format: png, jpg, jpeg, webp (default: png)",
        enum: ["png", "jpg", "jpeg", "webp"]
      }
    },
    required: ["website_url"]
  }
};
```

#### Tool Implementation

```typescript
interface BrandConsistentVisualsInput {
  website_url: string;
  custom_description?: string;
  brand_logo?: string;
  brand_logos?: string[];
  brand_info?: string;
  brand_memory?: {
    name?: string;
    overview?: string;
    tagline?: {
      text?: string;
      tones?: string[];
    };
    palette?: {
      primary?: Array<{ hex: string; label: string }>;
      secondary?: Array<{ hex: string; label: string }>;
    };
    fonts?: Array<{
      family?: string;
      weight?: string;
      size?: string;
      color?: string;
    }>;
  };
  num_variations?: number;
  aspect_ratio?: string;
  output_format?: string;
}

interface BrandConsistentVisualsOutput {
  success: boolean;
  outputAssets: Array<{
    type: "image";
    url: string;
    metadata: {
      prompt: string;
      aspect_ratio: string;
      model_used: string;
      generation_time: number;
    };
  }>;
  error?: string;
}

async function generateBrandConsistentVisuals(
  input: BrandConsistentVisualsInput
): Promise<BrandConsistentVisualsOutput> {
  try {
    // Call your ShopOS workflow API endpoint
    const response = await fetch("https://api.shopos.com/workflow/brand-kit/execute", {
      method: "POST",
      headers: {
        "Content-Type": "application/json",
        "Authorization": `Bearer ${process.env.SHOPOS_API_KEY}`
      },
      body: JSON.stringify({
        website_url: input.website_url,
        custom_description: input.custom_description || "",
        brand_logo: input.brand_logo || "",
        brand_logos: input.brand_logos || [],
        brand_info: input.brand_info || "",
        brand_memory: input.brand_memory || null,
        num_variations: input.num_variations || 3,
        aspect_ratio: input.aspect_ratio || "1:1",
        output_format: input.output_format || "png"
      })
    });

    if (!response.ok) {
      throw new Error(`API request failed: ${response.statusText}`);
    }

    const result = await response.json();
    return result;
  } catch (error) {
    console.error("Error generating brand-consistent visuals:", error);
    return {
      success: false,
      outputAssets: [],
      error: error instanceof Error ? error.message : "Unknown error occurred"
    };
  }
}
```

#### Agent Usage Example

```typescript
import Anthropic from "@anthropic-ai/sdk";

const anthropic = new Anthropic({
  apiKey: process.env.ANTHROPIC_API_KEY
});

async function runBrandVisualsAgent(userMessage: string) {
  const messages: Anthropic.MessageParam[] = [
    {
      role: "user",
      content: userMessage
    }
  ];

  let continueLoop = true;

  while (continueLoop) {
    const response = await anthropic.messages.create({
      model: "claude-3-5-sonnet-20241022",
      max_tokens: 4096,
      tools: [brandConsistentVisualsTool],
      messages
    });

    console.log("Assistant:", response.content);

    // Check if Claude wants to use the tool
    if (response.stop_reason === "tool_use") {
      const toolUse = response.content.find(
        (block): block is Anthropic.ToolUseBlock => block.type === "tool_use"
      );

      if (toolUse && toolUse.name === "generate_brand_consistent_visuals") {
        console.log("Generating brand-consistent visuals...");
        
        const result = await generateBrandConsistentVisuals(
          toolUse.input as BrandConsistentVisualsInput
        );

        // Add assistant's response and tool result to messages
        messages.push({
          role: "assistant",
          content: response.content
        });

        messages.push({
          role: "user",
          content: [
            {
              type: "tool_result",
              tool_use_id: toolUse.id,
              content: JSON.stringify(result)
            }
          ]
        });

        // Continue the loop to get Claude's final response
        continue;
      }
    }

    // If we get here, Claude has finished
    continueLoop = false;
  }
}

// Example usage
runBrandVisualsAgent(
  "Create social media content for Urban Vault fashion brand. Their website is https://urbanvault.fashion. Generate 4 variations in 1:1 format."
);
```

## Best Practices

### Brand Analysis
- Always use website_url as the primary source of truth for brand understanding
- Analyze brand positioning, not just surface-level products
- Consider brand maturity, target audience, and visual culture
- Extract strategic insights, don't just summarize inputs

### Creative Brief Quality
- Create agency-quality briefs with clear WHAT, WHY, and HOW
- Include specific visual direction and design principles
- Reference brand colors, typography, and guidelines
- Avoid generic or vague language - be specific and actionable

### Prompt Generation
- Write 150-300 words of detailed visual description per prompt
- Ensure complete uniqueness - no repeating backgrounds or copy
- Use professional photography and design terminology
- Specify realistic physics and proper spatial relationships
- Include technical specs: lighting, camera, composition, colors

### Visual Consistency
- Maintain brand personality across all variations
- Use brand colors, fonts, and visual language consistently
- Ensure each output feels authentically "on-brand"
- Balance consistency with creative diversity

### Quality Standards
- Verify all generated images have valid URLs
- Check for generation errors in metadata
- Ensure proper aspect ratios and output formats
- Filter out failed generations before returning results

## Common Pitfalls

1. **Generic Creative Briefs**: Avoid vague terms like "modern" or "dynamic" - be specific about visual direction
2. **Repeating Backgrounds**: Each prompt must have a unique environment - no concrete walls/floors
3. **Repeating Copy**: Each prompt must use different descriptive language and visual narrative
4. **Ignoring Brand Positioning**: Don't just use brand colors - understand the strategic positioning
5. **Hallucinations**: Specify realistic physics, proper grounding, no floating objects
6. **Poor Text Placement**: Design text integration carefully - proper alignment, spacing, hierarchy
7. **Inconsistent Brand Voice**: Maintain brand personality across all variations
8. **Missing Website Analysis**: Always analyze the website thoroughly - it's the primary source

## Related Skills

- **product-background-generation**: For product-specific brand visualizations
- **fashion-model-photography**: For fashion brand editorial content
- **garment-lifestyle-photography**: For lifestyle brand photography
- **product-analysis-styling**: For product-focused brand assets

## Technical Notes

- Uses Gemini 3 Pro Image Preview for generation (or equivalent model)
- Supports concurrent generation of multiple variations for efficiency
- Converts brand logos to S3 URLs for reliable image generation
- Extracts aspect ratios from prompts when using "default" setting
- Validates all inputs (aspect ratios, output formats) before generation
- Implements comprehensive error handling and retry logic
- Publishes progress events for real-time status updates

## Version History

- **1.0.0** (2024-01): Initial release with multi-step brand analysis and professional prompt generation

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tara-shopos) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
