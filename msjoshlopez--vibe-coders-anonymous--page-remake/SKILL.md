---
name: page-remake
description: Remake and improve existing web pages from URL examples. Use when user provides a URL and asks to "remake", "rebuild", "recreate", or "use as inspiration" for their site. Screenshots the original, analyzes branding, and rebuilds section-by-section. Use when this capability is needed.
metadata:
  author: msjoshlopez
---

# Page Remake Skill

This skill transforms existing web pages into your own version. It captures the essence of the original and rebuilds it in your codebase.

---

## Important: Legal & Ethical Guidelines

**When remaking a site, you MUST:**

- Create ORIGINAL content inspired by the reference (never copy text verbatim)
- Design your OWN visual elements (never copy logos, icons, or trademarked images)
- Write FRESH copy that captures the tone but uses different words
- Use your own imagery (placeholders or licensed stock photos)

**NEVER copy:**

- Logos or brand marks
- Trademarked content or slogans
- Proprietary icons or illustrations
- Exact copy/text word-for-word
- Unique visual assets that belong to the original site

**The goal is to learn from great design, not plagiarize it.**

---

## When to Trigger

**Automatically run this skill when user says:**

- "Remake this page: [URL]"
- "Start from this example: [URL]"
- "I want my site to look like this: [URL]"
- "Rebuild this: [URL]"
- "Use this as inspiration: [URL]"
- "Make my site like [URL]"
- "Copy the style of [URL]"
- "Recreate this: [URL]"

**Also trigger when:**

- User shares a URL and asks you to "replicate", "mirror", or "build something similar"
- User says "I like this website" followed by a URL
- User provides a screenshot and says "make it like this"

---

## Prerequisites

Before starting, determine how to analyze the target page:

1. **Primary Method (WebFetch):** Always available - fetches and analyzes page content
2. **Optional (Playwright MCP):** If configured in `.mcp.json`, can capture visual screenshots

---

## Phase 1: Capture the Original

### Option A: Using WebFetch (Recommended - Always Available)

Use the `WebFetch` tool to analyze the page design:

```
WebFetch with:
- url: [the target URL]
- prompt: "Analyze this page's design in detail. Describe: 1) Color palette (hex values if visible), 2) Typography style, 3) Layout structure section by section, 4) Visual elements and decorations, 5) Navigation style, 6) Footer content. Be specific and thorough."
```

### Option B: Using Playwright MCP (If Configured)

If Playwright MCP tools are available, you can capture visual screenshots for reference.

**Checking for Playwright availability:**

1. Look in `.mcp.json` for a Playwright server configuration
2. If configured, look for tools like:
   - `mcp__playwright__navigate` - Navigate to a URL
   - `mcp__playwright__screenshot` - Capture screenshots
   - `mcp__playwright__browser_action` - General browser control

**If Playwright is NOT configured:**

- Use Option A (WebFetch) instead—it's always available and works well for design analysis
- WebFetch extracts page structure, colors, and layout patterns from HTML/CSS
- For visual reference, ask the user to provide screenshots manually

**If Playwright IS configured:**

```
1. Navigate to the target URL
2. Capture a full-page screenshot
3. Save to static/references/original-[sitename]-[date].png
```

> **Recommendation:** WebFetch (Option A) is usually sufficient for understanding page structure and branding. Playwright screenshots are a nice-to-have for visual reference but not required.

### Screenshot Storage

Create the references directory if screenshots are captured:

```
static/
└── references/
    └── original-[sitename]-[date].png
```

**Tell the user:** "I'm analyzing [URL] to study its design. This will be my reference as I rebuild each section."

---

## Phase 1.5: Choose Your Approach

After capturing the screenshot, ask the user which approach they prefer:

> "I've captured the original. How should I approach this remake?
>
> **Option 1: Exact Remake**
> Recreate each section as closely as possible—same structure, layout, content patterns.
> Best if: You love the original and just need it in your codebase.
>
> **Option 2: Same Brand, Fresh Build**
> Keep the fonts, colors, and brand feel. May reorganize or improve layouts.
> Best if: You want the brand identity but are open to improvements.
>
> **Option 3: Inspired Remake** (Recommended)
> Capture the essence but apply thoughtful design choices—distinctive fonts, optimized layouts, better copy.
> Best if: You want something that feels similar but with intentional improvements."

Wait for the user's choice before proceeding.

---

## Phase 2: Analyze & Create Brand Document

After capturing the screenshot, perform a detailed visual analysis.

### What to Analyze

Study the screenshot carefully and document:

1. **Color Palette**
   - Primary color (dominant brand color)
   - Secondary colors (supporting tones)
   - Accent color (CTAs, highlights)
   - Background colors (sections, cards)
   - Text colors (headings vs body)

2. **Typography**
   - Heading font style (serif, sans-serif, display)
   - Body text style
   - Font weights used
   - Letter spacing patterns
   - Text sizes hierarchy

3. **Layout Patterns**
   - Hero style (full-width, split, minimal)
   - Section structures (how content is organized)
   - Grid patterns (columns, asymmetry)
   - Spacing rhythm (tight, generous, varied)
   - Visual flow (Z-pattern, F-pattern, scrolling)

4. **Visual Elements**
   - Image treatment (photography style, filters)
   - Icons (style, consistency, usage)
   - Decorative elements (shapes, lines, backgrounds)
   - Buttons/CTAs (shape, style, hover states)

5. **Brand Messaging**
   - Tone of voice (formal/casual, serious/playful)
   - Key value propositions
   - Target audience signals
   - Emotional appeal

6. **Section Inventory**
   - List each section from top to bottom
   - Note the purpose of each section
   - Document layout/structure of each

### Create BRAND-ANALYSIS.md

Save this to the project root with a detailed analysis (see full template in the skill).

---

## Phase 3: Handle SITE.md

Before building, check for existing SITE.md and ask the user how to proceed.

### If SITE.md EXISTS

Ask the user:

> "I found an existing SITE.md with your project information. How would you like to proceed?
>
> **Option A: Merge** - I'll blend insights from [URL] with your existing brand identity
>
> **Option B: Fresh Start** - I'll create a new SITE.md based entirely on [URL]'s brand analysis
>
> Which do you prefer?"

### If SITE.md DOES NOT EXIST

Create SITE.md using the brand analysis as the visual foundation, but ask key business questions first.

---

## Phase 4: Section-by-Section Rebuild

Build each section one at a time, referencing the original screenshot.

### Approach-Specific Guidelines

#### For Exact Remake

- Replicate layouts faithfully
- Match colors as closely as possible
- Find closest Google Font matches for typography
- Preserve content structure and hierarchy

#### For Same Brand, Fresh Build

- Use original fonts/colors exactly or find closest matches
- Preserve the overall brand feel
- May suggest layout improvements if beneficial

#### For Inspired Remake (Recommended)

- Full creative freedom with design choices
- Apply human-first design principles
- Choose distinctive fonts that match the brand feel
- Optimize layouts for clarity and impact

### For EACH Section:

1. **Reference the Original** - Look at layout, content, visual treatment
2. **Apply Selected Approach Rules** - Match faithfully or improve
3. **Write Copy** - Use copywriting skill guidelines
4. **Implement the Section** - Use svelte-sveltekit-expert patterns

### Section Build Order

1. **Hero** - Sets the tone for everything
2. **Social Proof Bar** - Early credibility (if original has one)
3. **Main Content Sections** - Features, benefits, how it works
4. **Testimonials** - Social proof
5. **Final CTA** - Conversion section
6. **Navbar** - Navigation (do this alongside hero)
7. **Footer** - Links and secondary info

---

## Phase 5: Quality Verification

After rebuilding all sections, compare with the original and verify the approach-specific requirements were met.

### Checklist

**For All Approaches:**

- [ ] Brand feel is captured
- [ ] Colors work well together
- [ ] Typography hierarchy is clear
- [ ] Copy is specific and human-sounding
- [ ] Mobile responsive
- [ ] Accessible (proper headings, alt text, contrast)

---

## Integration with Other Skills

This skill orchestrates multiple other skills:

| Skill                       | When to Use                                    |
| --------------------------- | ---------------------------------------------- |
| **brand-identity**          | Check all color/font choices                   |
| **copywriting**             | Write all text content (headlines, body, CTAs) |
| **marketing-site-design**   | Section architecture and conversion patterns   |
| **frontend-design**         | Visual implementation and creative direction   |
| **svelte-sveltekit-expert** | Code implementation patterns                   |
| **documentation-writer**    | Update SITE.md after completion                |

---

## Files Created

| File                | Location                     | Purpose                               |
| ------------------- | ---------------------------- | ------------------------------------- |
| `BRAND-ANALYSIS.md` | Project root                 | Detailed analysis of original         |
| Original screenshot | `static/references/`         | Visual reference during build         |
| `SITE.md`           | Project root                 | Updated/created project documentation |
| Page components     | `src/routes/` and `src/lib/` | The rebuilt page                      |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/msjoshlopez) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
