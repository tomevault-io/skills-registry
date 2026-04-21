---
name: md-to-pptx
description: Convert Markdown documents to PowerPoint presentations or generate presentations from scratch using AI. Use when users want to create PPT/PPTX files, convert MD to slides, generate presentations, make slideshows, or ask for help with PowerPoint creation. Supports custom templates, multiple themes (business, tech_dark, education, neumorphism), and intelligent content layout. Use when this capability is needed.
metadata:
  author: echoleesong
---

# md-to-pptx

Convert Markdown to PowerPoint or generate presentations with AI assistance.

## Dependencies

Install required packages before use:

```bash
pip install python-pptx Pillow
```

**Note**: Use `python3` instead of `python` on systems where Python 3 is not the default.

## Workflow

### Step 1: Gather Requirements

**IMPORTANT**: Before generating any content, you MUST ask the user the following questions using the AskUserQuestion tool:

1. **PPT页数 (Slide Count)**: 您希望PPT包含多少页？
   - Options: 5-10页, 10-15页, 15-20页, 20页以上

2. **面向用户群体 (Target Audience)**: 这个PPT的目标受众是谁？
   - Options: 企业高管/决策者, 技术团队/开发者, 学生/教育场景, 普通大众/客户

3. **PPT演示场景 (Presentation Context)**: 这个PPT将在什么场景下使用？
   - Options: 商务汇报/会议, 产品发布/路演, 培训/教学, 学术报告/研究分享

4. **PPT模板选择 (Template Selection)**: 是否使用已有的PPT模板？
   - Options:
     - business (商务风格 - 白色背景，深蓝标题)
     - tech_dark (科技风格 - 深色背景，绿色点缀)
     - education (教育风格 - 暖色背景，橙色点缀)
     - neumorphism (新拟态风格 - 蓝黄色现代风格)
     - 不使用模板/自定义

Additional questions to ask:

5. **Source**: Do you have an existing Markdown file, or should I generate content?
6. **Save location**: Where to save the PPT? (default: user's current working directory)

Use the answers to guide content generation:
- **Slide Count**: Determines the depth and detail of content
- **Target Audience**: Affects language complexity, terminology, and examples
- **Presentation Context**: Influences tone, structure, and visual style
- **Template**: Sets the visual theme for the presentation

### Step 2: Determine Approach

**If converting existing Markdown:**
1. Read the Markdown file
2. Parse content into slides (H1/H2 headings create new slides)
3. Generate PPTX with appropriate layouts

**If generating new content:**
1. Create an outline based on user's topic
2. Present outline for user approval
3. Generate full Markdown content
4. Convert to PPTX

### Step 3: Generate Presentation

Run the conversion script:

```bash
python scripts/md_to_pptx.py input.md -o output.pptx --theme business
```

## Command Line Options

```bash
python scripts/md_to_pptx.py <input.md> [options]

Options:
  -o, --output      Output filename (default: input name + .pptx)
  -d, --directory   Output directory (default: user's current directory)
  -t, --theme       Theme: business, tech_dark, education, neumorphism
  --template        Path to custom .pptx template
  --no-template     Generate without using built-in template
  -q, --quiet       Suppress progress output
  --list-themes     Show available themes
  --list-templates  Show template locations
```

### Examples

```bash
# Save to current directory (default)
python scripts/md_to_pptx.py presentation.md

# Specify output filename
python scripts/md_to_pptx.py presentation.md -o slides.pptx

# Save to Desktop
python scripts/md_to_pptx.py presentation.md -d ~/Desktop

# Use tech dark theme
python scripts/md_to_pptx.py presentation.md --theme tech_dark

# Use neumorphism theme
python scripts/md_to_pptx.py presentation.md --theme neumorphism

# Use custom template
python scripts/md_to_pptx.py presentation.md --template company.pptx

# Show available themes
python scripts/md_to_pptx.py --list-themes

# Show template locations
python scripts/md_to_pptx.py --list-templates
```

## Built-in Templates

Four pre-built templates are included in `assets/templates/`:

| Template | File | Description |
|----------|------|-------------|
| business | `business_template.pptx` | White background, dark blue titles, professional |
| tech_dark | `tech_dark_template.pptx` | Dark gray background, green accents, developer-friendly |
| education | `education_template.pptx` | Warm cream background, orange accents, approachable |
| neumorphism | `蓝黄色新拟态行业调研报告PPT模板.pptx` | Blue-yellow neumorphism style, modern research report |

### Custom Templates

Place custom `.pptx` templates in `assets/templates/` or specify path with `--template`:

```bash
# Use template from assets/templates/
python scripts/md_to_pptx.py input.md --template assets/templates/my_company.pptx

# Use template from any location
python scripts/md_to_pptx.py input.md --template /path/to/custom.pptx
```

To regenerate built-in templates:
```bash
python scripts/generate_templates.py -o assets/templates
```

## Markdown Format

Key rules: `# H1` creates title slide, `## H2` creates content slides, `### H3+` stays on current slide.

For detailed format reference including supported elements, image handling, and layout selection, see [references/markdown-format.md](references/markdown-format.md).

## Error Handling

The generator uses fault-tolerant mode:
- Unsupported elements are skipped with warnings
- Missing images show placeholder text
- Malformed tables fall back to text
- All warnings are collected and reported at the end

## Progress Feedback

The script reports progress per slide:
```
📄 Reading: presentation.md
📊 Parsed 6 slides
📋 Using built-in template: tech_dark_template.pptx
[████████░░░░░░░░░░░░░░░░░░░░░░] 3/6 - Generating slide 3: Introduction...
✅ Created: /path/to/output.pptx
```

## Python API

```python
from scripts.md_to_pptx import convert_md_to_pptx, generate_from_content

# Convert file
success, output_path, warnings = convert_md_to_pptx(
    "presentation.md",
    output_path="slides.pptx",
    theme="business"
)

# Generate from content string
success, warnings = generate_from_content(
    "# My Title\n\n## Slide 1\nContent here",
    "output.pptx",
    theme="tech_dark"
)
```

## Typical Interaction Flow

1. User: "Help me create a presentation about X"
2. Ask: Theme preference? Save location?
3. Generate outline, present for approval
4. User approves or requests changes
5. Generate full Markdown content
6. Run conversion script with chosen options
7. Report: Created X slides, saved to Y, any warnings

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/echoleesong) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
