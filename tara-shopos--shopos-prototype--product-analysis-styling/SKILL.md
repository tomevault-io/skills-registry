---
name: product-analysis-styling
description: Analyze product images to extract attributes (category, style, materials, colors, design details) and generate appropriate styling recommendations for photography. Use when preparing product shoots, fashion photography, or creating styled product compositions. Use when this capability is needed.
metadata:
  author: tara-shopos
---

# Product Analysis and Styling

## When to Use This Skill

Use this skill when you need to:
- Analyze product images before creating photography
- Extract product attributes for accurate representation
- Generate styling recommendations for product shoots
- Determine appropriate complementary items
- Understand product positioning and target audience
- Create cohesive styled product compositions

## Core Concepts

### Three-Level Material Specificity

Always analyze materials with three levels:
1. **Base Material**: Cotton, leather, polyester, metal, ceramic
2. **Construction**: Weave type, grain pattern, metal type
3. **Surface Finish**: Texture, treatment, appearance

Example: "cotton denim with right-hand twill weave and stone-washed matte finish"

### Analysis Categories

**Product Classification:**
- Category (top, bottom, fullbody, accessory, home goods)
- Specific type (silk blouse, leather boots, ceramic vase)
- Gender/target demographic
- Age category (infant, child, teen, adult)

**Style Assessment:**
- Style classification (casual, formal, sporty, elegant, minimalist)
- Occasion suitability
- Brand positioning (budget, mid-market, premium, luxury)

**Material Analysis:**
- Base materials
- Construction methods
- Surface treatments and finishes

**Design Details:**
- Silhouette and fit
- Key design elements
- Construction details
- Brand indicators

## Step-by-Step Instructions

### Step 1: Visual Product Analysis

Examine product images to identify:
- Product category and specific type
- Gender/target demographic
- Age category
- Style classification
- Occasion suitability

### Step 2: Material and Construction Analysis

Apply three-level specificity:
- Identify base materials
- Determine construction methods
- Assess surface finishes and treatments

### Step 3: Color and Pattern Analysis

Extract:
- Primary colors (specific names: "navy blue" not "blue")
- Secondary/accent colors
- Pattern type (solid, striped, floral, geometric)
- Color temperature (warm, cool, neutral)
- Finish (matte, glossy, metallic)

### Step 4: Design Details Extraction

Document:
- Silhouette and fit characteristics
- Key design elements (buttons, zippers, pockets)
- Construction details (stitching, seams, hardware)
- Brand indicators and distinctive features

### Step 5: Generate Styling Recommendations

Based on analysis, recommend:

**For Garments:**
- Complementary topwear (if bottom analyzed)
- Complementary bottomwear (if top analyzed)
- Appropriate footwear (specific style and color)
- Accessories (minimal, statement, or none)
- Overall styling approach

**For Accessories:**
- Outfit context (what to pair with)
- Styling placement (how to wear/display)
- Complementary pieces
- Occasion suitability

**For Products:**
- Display context (environment, props)
- Complementary items
- Lifestyle integration

### Step 6: Create Structured Output

Format as JSON:
```json
{
  "product_category": "top|bottom|fullbody|accessory",
  "product_type": "specific item name",
  "gender": "male|female|unisex",
  "age_category": "infant|child|teen|adult",
  "style_classification": "casual|formal|sporty|elegant",
  "materials": {
    "base": "primary material",
    "construction": "weave/grain/type",
    "finish": "surface treatment"
  },
  "colors": {
    "primary": "specific color name",
    "secondary": ["color1", "color2"],
    "temperature": "warm|cool|neutral"
  },
  "design_details": ["feature1", "feature2"],
  "styling_recommendations": {
    "topwear": "recommendation or N/A",
    "bottomwear": "recommendation or N/A",
    "footwear": "specific style and color",
    "accessories": "minimal|statement|none",
    "overall_approach": "styling philosophy"
  }
}
```

## Examples

### Example 1: Denim Jacket Analysis

**Input:** Image of blue denim jacket

**Analysis Output:**
```json
{
  "product_category": "top",
  "product_type": "denim trucker jacket",
  "gender": "unisex",
  "age_category": "adult",
  "style_classification": "casual",
  "materials": {
    "base": "cotton denim",
    "construction": "right-hand twill weave",
    "finish": "stone-washed with slight fading, matte surface"
  },
  "colors": {
    "primary": "medium indigo blue",
    "secondary": ["brass hardware", "white contrast stitching"],
    "temperature": "cool"
  },
  "design_details": [
    "Classic trucker silhouette with pointed collar",
    "Brass button closure and rivets",
    "Two chest pockets with button flaps",
    "Adjustable side tabs at waist"
  ],
  "styling_recommendations": {
    "topwear": "N/A (this is the top)",
    "bottomwear": "Black or dark grey slim-fit chinos, or light wash jeans for contrast",
    "footwear": "White leather sneakers or tan suede desert boots",
    "accessories": "Minimal - simple watch, no competing denim",
    "overall_approach": "Classic casual Americana - keep styling clean and unfussy, let the jacket be the statement piece"
  }
}
```

### Example 2: Statement Earrings Analysis

**Input:** Image of gold chandelier earrings

**Analysis Output:**
```json
{
  "product_category": "accessory",
  "product_type": "chandelier drop earrings",
  "gender": "female",
  "age_category": "adult",
  "style_classification": "elegant",
  "materials": {
    "base": "gold-plated brass",
    "construction": "multi-tier chandelier structure",
    "finish": "polished with high shine"
  },
  "colors": {
    "primary": "warm gold",
    "secondary": ["clear crystal accents"],
    "temperature": "warm"
  },
  "design_details": [
    "Three-tier cascading design with crystal embellishments",
    "Approximately 3 inches in length",
    "Post back closure",
    "Intricate filigree metalwork"
  ],
  "styling_recommendations": {
    "topwear": "N/A",
    "bottomwear": "N/A",
    "footwear": "N/A",
    "accessories": "Keep other jewelry minimal - these are statement pieces",
    "overall_approach": "Pair with updo or swept-back hair to showcase earrings. Wear with elegant neckline (off-shoulder, V-neck, or strapless) in solid colors. Avoid competing patterns or necklaces. Suitable for formal events, weddings, or evening occasions."
  }
}
```

### Example 3: Ceramic Vase Analysis

**Input:** Image of white ceramic vase

**Analysis Output:**
```json
{
  "product_category": "home_decor",
  "product_type": "minimalist ceramic vase",
  "gender": "unisex",
  "age_category": "adult",
  "style_classification": "minimalist",
  "materials": {
    "base": "ceramic stoneware",
    "construction": "wheel-thrown with hand-finished rim",
    "finish": "matte white glaze with subtle texture"
  },
  "colors": {
    "primary": "warm off-white",
    "secondary": [],
    "temperature": "warm neutral"
  },
  "design_details": [
    "Organic asymmetrical form",
    "Narrow neck opening to wide body",
    "Approximately 10 inches tall",
    "Visible throwing lines add handcrafted character"
  ],
  "styling_recommendations": {
    "topwear": "N/A",
    "bottomwear": "N/A",
    "footwear": "N/A",
    "accessories": "N/A",
    "overall_approach": "Display on natural wood surface or light-colored shelf. Pair with single stem or small dried arrangement - avoid overcrowding. Complement with other neutral tones and natural materials. Suitable for Scandinavian, minimalist, or modern organic interiors. Photograph with soft natural light and clean background."
  }
}
```

## Key Principles

1. **Precision Over Generalization**: "Navy blue cotton twill" not "blue pants"
2. **Three-Level Material Specificity**: Always base + construction + finish
3. **Actionable Recommendations**: Specific items, not vague suggestions
4. **Style Consistency**: Recommendations match product's aesthetic level
5. **Avoid Redundancy**: Don't recommend competing items
6. **Context Awareness**: Consider occasion, season, target audience

## Common Mistakes to Avoid

- ❌ Generic descriptions: "nice fabric" instead of specific material
- ❌ Vague colors: "blue" instead of "navy blue" or "cobalt blue"
- ❌ Missing construction details: "leather" instead of "full-grain leather with pebbled finish"
- ❌ Inconsistent styling: Recommending formal shoes with casual garment
- ❌ Over-styling: Too many competing elements
- ❌ Ignoring target audience: Adult styling for children's products

## Integration Pattern

```python
# Analyze product
analysis = await analyze_product(
    product_images=["url1", "url2"],
    model_category="default"  # or "male", "female", "child"
)

# Use analysis for prompt generation
prompt = f"""
Professional fashion photography of {analysis['product_type']}.

PRODUCT DETAILS:
- Material: {analysis['materials']['base']} with {analysis['materials']['finish']}
- Color: {analysis['colors']['primary']}
- Style: {analysis['style_classification']}

STYLING:
- {analysis['styling_recommendations']['bottomwear']}
- {analysis['styling_recommendations']['footwear']}
- Accessories: {analysis['styling_recommendations']['accessories']}

{analysis['styling_recommendations']['overall_approach']}

Shot on professional camera, editorial quality, 8K resolution.
"""

# Generate image
result = await image_gen(
    prompt=prompt,
    images=[{"url": product_image, "name": "Product"}],
    aspect_ratio="2:3"
)
```

## Output Schema

```python
from pydantic import BaseModel, Field
from typing import List, Optional
from enum import Enum

class GarmentCategory(str, Enum):
    TOP = "top"
    BOTTOM = "bottom"
    FULLBODY = "fullbody"
    ACCESSORY = "accessory"

class Gender(str, Enum):
    MALE = "male"
    FEMALE = "female"
    UNISEX = "unisex"

class StyleCategory(str, Enum):
    CASUAL = "casual"
    FORMAL = "formal"
    SPORTY = "sporty"
    ELEGANT = "elegant"
    MINIMALIST = "minimalist"

class StylingRecommendations(BaseModel):
    topwear: str
    bottomwear: str
    footwear: str
    accessories: str
    overall_approach: str

class ProductAnalysis(BaseModel):
    product_category: GarmentCategory
    product_type: str
    gender: Gender
    age_category: str
    style_classification: StyleCategory
    materials: dict
    colors: dict
    design_details: List[str]
    styling_recommendations: StylingRecommendations
```

## References

- Source: `workflow_garments_v2/implementation/utils/garment_analysis.py`
- Related Skills: product-background-generation, fashion-model-photography
- Material Terminology Guide: See references/materials.md

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tara-shopos) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
