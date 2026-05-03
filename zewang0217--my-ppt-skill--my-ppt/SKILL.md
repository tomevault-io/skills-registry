---
name: my-ppt
description: Create aesthetically pleasing PowerPoint presentations with multiple preset styles and presentation types. Use when Claude needs to create beautiful, professional presentations (.pptx files) with: (1) Multiple aesthetic styles (tech, simple, elegant), (2) Multiple presentation types (report, record, presentation), (3) NotebookLM-style visual quality, (4) Customizable design elements Use when this capability is needed.
metadata:
  author: zewang0217
---

# Aesthetic PPT Generator

## Overview

Create beautiful, professional PowerPoint presentations with multiple aesthetic styles and presentation types. This skill provides pre-configured design systems inspired by NotebookLM's visual quality, enabling quick generation of aesthetically pleasing presentations without manual design work.

## Available Styles

### Tech Style (科技风)
Modern, futuristic design with deep backgrounds and neon accents. Best for technology presentations, startup pitches, and innovation showcases.

### Simple Style (简单风)
Minimalist, clean design with white backgrounds and clear typography. Best for business presentations, academic slides, and documentation.

### Elegant Style (优雅风)
Sophisticated design with soft tones and refined details. Best for luxury brand presentations, artistic showcases, and lifestyle content.

See [styles.md](references/styles.md) for detailed design principles, color palettes, and customization guidelines.

## Available Presentation Types

### Report Type (汇报PPT)
Data visualization, bullet lists, summary pages, and charts. Ideal for business reports, analytics, and executive summaries.

### Record Type (记录PPT)
Timeline, note format, and documented structure. Ideal for meeting minutes, project documentation, and knowledge base entries.

### Presentation Type (展示PPT)
Large images, visual impact, and storytelling. Ideal for product launches, creative portfolios, and marketing presentations.

See [types.md](references/types.md) for detailed structure, content organization, and best practices for each type.

## Workflow

### Step 1: Choose Style and Type

Determine the appropriate combination based on your content and audience:

**Style Selection**:
- **Tech**: Technology products, innovation, modern aesthetics
- **Simple**: Business reports, academic content, clean presentations
- **Elegant**: Luxury brands, artistic content, refined presentations

**Type Selection**:
- **Report**: Data-driven, stakeholder presentations, executive summaries
- **Record**: Meeting minutes, documentation, knowledge management
- **Presentation**: Product launches, marketing, visual storytelling

### Step 2: Generate or Copy Templates

**Using Existing Templates**:
```bash
# List available templates
python scripts/generate_template.py list-templates

# Copy a specific template
python scripts/generate_template.py use-template <template-name> [output-dir]

# Example: Copy tech title template
python scripts/generate_template.py use-template tech-title ./slides
```

**Generating from Style and Type**:
```bash
# Generate tech report templates
python scripts/generate_template.py generate tech report ./slides

# Generate simple presentation templates
python scripts/generate_template.py generate simple presentation ./slides

# Generate elegant record templates
python scripts/generate_template.py generate elegant record ./slides
```

This generates HTML template files and gradient backgrounds (if applicable) in the specified output directory.

### Step 3: Customize HTML Templates

Edit the generated HTML files to add your content:

**Key Editing Points**:
- Replace placeholder text with actual content
- Modify bullet points in `<ul>` lists
- Adjust headings in `<h1>`, `<h2>`, `<h3>` tags
- Update dates and author information
- Add or remove content sections as needed

**Important Rules**:
- All text MUST be in `<p>`, `<h1>`-`<h6>`, `<ul>`, or `<ol>` tags
- Use web-safe fonts only: Arial, Helvetica, Times New Roman, Georgia, Courier New, Verdana, Tahoma, Trebuchet MS, Impact
- Maintain proper body dimensions: `width: 720pt; height: 405pt` for 16:9 layout
- Use `class="placeholder"` for areas where charts/images will be added

### Step 4: Convert HTML to PowerPoint

Use the html2pptx library to convert HTML slides to PowerPoint:

```javascript
const pptxgen = require('pptxgenjs');
const html2pptx = require('./scripts/html2pptx.js');

async function createPresentation() {
  const pptx = new pptxgen();
  pptx.layout = 'LAYOUT_16x9';
  pptx.author = 'Your Name';
  pptx.title = 'Presentation Title';

  const htmlFiles = [
    'slides/1-title.html',
    'slides/2-agenda.html',
    'slides/3-content.html'
  ];

  for (const htmlFile of htmlFiles) {
    const { slide, placeholders } = await html2pptx(htmlFile, pptx);
    
    if (placeholders.length > 0) {
      slide.addChart(pptx.charts.BAR, chartData, placeholders[0]);
    }
  }

  await pptx.writeFile({ fileName: 'output.pptx' });
  console.log('Presentation created successfully!');
}

createPresentation().catch(console.error);
```

**Adding Charts**:
```javascript
const chartData = [{
  name: 'Sales',
  labels: ['Q1', 'Q2', 'Q3', 'Q4'],
  values: [4500, 5500, 6200, 7100]
}];

slide.addChart(pptx.charts.BAR, chartData, {
  ...placeholders[0],
  showTitle: true,
  title: 'Quarterly Sales',
  showCatAxisTitle: true,
  catAxisTitle: 'Quarter',
  showValAxisTitle: true,
  valAxisTitle: 'Sales ($000s)',
  chartColors: ['3B82F6']
});
```

### Step 5: Validate and Refine

Generate thumbnails to visually inspect the presentation:

```bash
python scripts/thumbnail.py output.pptx
```

Review the thumbnail image for:
- Text cutoff or overlap
- Positioning issues
- Contrast problems
- Layout inconsistencies

Adjust HTML templates and regenerate if issues are found.

## Quick Start Example

Complete workflow to create a tech-style report presentation:

```bash
# 1. Generate templates
cd my-ppt
python scripts/generate_template.py tech report ./workspace/slides

# 2. Edit templates with your content
# Edit workspace/slides/*.html files

# 3. Create conversion script (example using python-pptx)
cat > workspace/convert.py << 'EOF'
#!/usr/bin/env python3
from pptx import Presentation
from pptx.util import Inches, Pt
from pathlib import Path

def main():
    prs = Presentation()
    prs.slide_width = Inches(13.33)  # 16:9
    prs.slide_height = Inches(7.5)
    
    # Simple example - create a title slide
    slide_layout = prs.slide_layouts[0]
    slide = prs.slides.add_slide(slide_layout)
    title = slide.shapes.title
    title.text = "Tech Report"
    subtitle = slide.placeholders[1]
    subtitle.text = "Generated with My PPT Skill"
    
    # Save presentation
    prs.save("presentation.pptx")
    print("Presentation created successfully!")

if __name__ == "__main__":
    main()
EOF

# 4. Convert to PowerPoint
cd workspace
python convert.py

# 5. Validate output (using official pptx skill's thumbnail script)
python ../../skills/pptx/scripts/thumbnail.py presentation.pptx
```

## Customization

### Modifying Styles

Edit `scripts/style_presets.json` to adjust:
- Color palettes (primary, secondary, accent, highlight, text colors)
- Typography (heading, body, code fonts)
- Background type (solid, gradient) and colors
- Design elements (shapes, borders, shadows, gradients)

### Creating Custom Types

Edit `scripts/type_presets.json` to add new presentation types with custom slide structures.

### Adding New Templates

Create new HTML templates in `assets/templates/<style>/` directory and reference them in your conversion script.

## Resources

### scripts/
- `generate_template.js` - Template generator for creating HTML slides
- `style_presets.json` - Style configuration (colors, fonts, elements)
- `type_presets.json` - Type configuration (slide structures, elements)

### references/
- `styles.md` - Detailed style guide with design principles
- `types.md` - Detailed type guide with content organization

### assets/templates/
- `tech/` - Tech-style HTML templates
- `simple/` - Simple-style HTML templates
- `elegant/` - Elegant-style HTML templates

## Dependencies

Required dependencies (should already be installed):
- **python-pptx**: `pip install python-pptx` (for creating presentations)
- **pillow**: `pip install pillow` (for image processing)

## Best Practices

1. **Choose style based on audience and content** - Match aesthetic to presentation purpose
2. **Keep text concise** - Use bullet points, avoid long paragraphs
3. **Maintain consistency** - Use same style throughout presentation
4. **Validate output** - Always check thumbnails before finalizing
5. **Use high contrast** - Ensure text is readable against backgrounds
6. **Embrace white space** - Don't overcrowd slides, especially in simple style
7. **Use charts effectively** - Visualize data, don't just list numbers
8. **Tell a story** - Structure content logically with clear narrative flow

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/zewang0217) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
