---
name: zensical-development
description: Best practices for developing zensical/Material for MkDocs static sites. Covers installation, virtual environments, project structure, responsive images, CSS, media embedding, and verification. Use for ANY Material for MkDocs project development beyond basic text editing. Use when this capability is needed.
metadata:
  author: zeulewan
---

# Zensical Development Best Practices

Comprehensive workflow and best practices for developing zensical (Material for MkDocs) static sites with proper setup, responsive design, image handling, and media embedding.

## ⚠️ CRITICAL: Always Use Zensical, Never MkDocs Directly

**NEVER use `mkdocs serve` or `mkdocs build` commands directly.**

Always use the zensical CLI:
- `zensical serve` (NOT `mkdocs serve`)
- `zensical build` (NOT `mkdocs build`)

Even if a project has an `mkdocs.yml` file, use zensical commands. Zensical is the proper toolchain for these projects.

## 🚀 Project Setup & Requirements

### Installation & Virtual Environment

**CRITICAL:** Every Zensical project MUST have a Python virtual environment (`.venv`).

```bash
# Create virtual environment
python3 -m venv .venv

# Activate virtual environment
source .venv/bin/activate  # macOS/Linux
# or
.venv\Scripts\activate     # Windows

# Install zensical
pip install zensical
```

**Alternative installation with uv:**
```bash
uv init
uv add zensical
```

### .gitignore Requirements

**MANDATORY entries for Zensical projects:**

```gitignore
# Python virtual environment
.venv/
venv/

# Zensical build output
site/

# Cache directories
.cache/

# System files
.DS_Store
```

### Creating a New Zensical Project

```bash
# Bootstrap new project
zensical new .

# This creates:
# - .github/ directory
# - docs/ folder with index.md and markdown.md
# - zensical.toml configuration file
```

### Configuration File (zensical.toml)

**Minimum required configuration:**

```toml
[project]
site_name = "Your Site Name"  # REQUIRED
site_url = "https://example.com/"  # Recommended for instant navigation
```

**Full configuration example:** See `/Users/zeul/GIT/machtmu.com/zensical.toml` for comprehensive setup including:
- Theme customization (`[project.theme]`)
- Plugins (`[project.plugins.*]`)
- Markdown extensions (`[project.markdown_extensions.*]`)
- Navigation (`[project] nav = [...]`)
- Social links (`[[project.extra.social]]`)
- Custom CSS/JS (`extra_css`, `extra_javascript`)

## ⚠️ CRITICAL REQUIREMENT

**For ANY changes beyond basic text editing to markdown files, you MUST:**

1. **Deploy the site** (`zensical serve` or `zensical build`)

2. **Visually verify using Chrome DevTools MCP:**
   - Navigate to the page: `navigate_page(url="http://127.0.0.1:8000/page/")`
   - Take screenshot: `take_screenshot()` to see actual rendering
   - Check computed styles: `evaluate_script()` to verify CSS is applied
   - Scroll and verify all sections render correctly

3. **Verify images specifically:**
   - Use `evaluate_script()` to check actual display dimensions
   - Compare max-width CSS vs actual rendered width
   - Ensure images aren't overflowing containers

**Example verification script:**
```javascript
() => {
  const img = document.querySelector('.image-container img');
  const computed = window.getComputedStyle(img);
  return {
    maxWidth: computed.maxWidth,
    displayWidth: img.offsetWidth,
    naturalWidth: img.naturalWidth
  };
}
```

**DO NOT assume changes work without visual verification. curl commands are NOT sufficient for CSS/layout changes.**

---

## Development Workflow

### Step 1: Make Changes

Identify what type of change you're making:

**Text-only changes** (no visual verification needed):
- Fixing typos
- Updating content paragraphs
- Changing headings/titles

**Visual changes** (REQUIRES visual verification):
- Adding/modifying CSS
- Adding/moving images
- Embedding videos
- Changing layouts
- Modifying HTML structure

### Step 2: Deploy Locally

```bash
# Start development server
source .venv/bin/activate  # or activate virtualenv
zensical serve

# Site runs at http://localhost:8000
```

**⚠️ CRITICAL: Server Restart & Cache Refresh Requirements**

After making ANY of the following changes, you MUST:
1. **Restart the zensical server** (Ctrl+C, then `zensical serve` again)
2. **Hard refresh the browser cache** to see the changes

**When to restart server:**
- ✅ CSS file changes (`docs/css/extra.css` or any stylesheet)
- ✅ Configuration changes (`zensical.toml` or `mkdocs.yml`)
- ✅ JavaScript file changes
- ✅ Theme template overrides
- ✅ Plugin configuration changes
- ⚠️ Sometimes needed after adding new images or pages

**When NOT needed (hot-reload works):**
- ✅ Markdown content edits (`.md` files)
- ✅ Simple text changes

**How to hard refresh browser cache:**

**Using Chrome MCP:**
```python
# Use navigate_page with ignoreCache parameter
navigate_page(type="reload", ignoreCache=True)
```

**Manual browser:**
- **macOS:** Cmd+Shift+R
- **Windows/Linux:** Ctrl+Shift+R or Shift+F5
- **Chrome DevTools:** Right-click reload button → "Empty Cache and Hard Reload"

**Why this matters:**
- Browsers cache CSS, JS, and images aggressively
- Zensical server must rebuild after CSS/config changes
- Without hard refresh, you'll see OLD cached versions
- This causes confusion when changes "don't appear to work"

**⚠️ DANGER: NEVER Kill Processes by Port**

**NEVER use commands like:**
```bash
# ❌ DANGEROUS - This can kill Firefox and other apps!
lsof -ti:8000 | xargs kill -9
kill $(lsof -ti:8000)
fuser -k 8000/tcp
```

These commands kill ALL processes on the port, which can terminate browsers (Firefox, Chrome) and other applications that happen to use that port.

**✅ Safe way to restart the server:**
1. Go to the terminal running `zensical serve`
2. Press `Ctrl+C` to stop gracefully
3. Run `zensical serve` again

If you started the server in background and need to kill it, find the specific process by name:
```bash
# Find zensical process
ps aux | grep zensical | grep -v grep

# Or kill by process name
pkill -f "zensical serve"
```

### Step 3: Verification (MANDATORY for visual changes)

**Using Chrome DevTools MCP (REQUIRED):**

```python
# 1. Navigate to page
mcp__chrome-devtools__navigate_page(url="http://127.0.0.1:8000/page/path/")

# 2. Take screenshot to see actual rendering
mcp__chrome-devtools__take_screenshot()

# 3. Verify image dimensions with script
mcp__chrome-devtools__evaluate_script(function="""
() => {
  const images = document.querySelectorAll('img');
  return Array.from(images).map(img => ({
    src: img.src.split('/').pop(),
    maxWidth: window.getComputedStyle(img).maxWidth,
    displayWidth: img.offsetWidth,
    naturalWidth: img.naturalWidth
  }));
}
""")

# 4. Scroll to check different sections
mcp__chrome-devtools__press_key(key="End")
mcp__chrome-devtools__take_screenshot()
```

**What to verify visually:**
- ✅ Hero/header images are properly constrained (not full-width)
- ✅ Grid layouts render in correct columns
- ✅ Images maintain aspect ratio (not stretched)
- ✅ No horizontal overflow
- ✅ Spacing and margins look correct

---

## Best Practices

### Responsive Images

**Global image CSS** (in `docs/css/extra.css` or custom CSS):

```css
/* ✅ BEST PRACTICE - Responsive images */
img {
    display: block;
    margin: 0 auto;
    max-width: 100%;  /* Never exceed container */
    height: auto;     /* Maintain aspect ratio */
}

/* ❌ AVOID - Fixed heights crop images */
img {
    max-height: 400px;  /* DON'T DO THIS */
    width: auto;
}
```

**Container classes for galleries:**

```css
/* Center single image */
.image-container {
    display: flex;
    justify-content: center;
}

.image-container img {
    max-width: 500px;  /* Optional max-width for large images */
    width: 100%;
}

/* Side-by-side images */
.image-row {
    display: flex;
    justify-content: center;
    gap: 20px;
}

.image-row img {
    max-width: 45%;
    height: auto;
}
```

### Image Paths

Images are referenced **relative to the markdown file location**.

**Example structure:**
```
docs/
└── projects/
    └── my-project/
        ├── my-project.md    ← Markdown file
        ├── image1.jpg       ← Same directory
        └── assets/          ← Subdirectory
            └── image2.jpg
```

**In `my-project.md`:**
```html
<!-- Same directory -->
<img src="image1.jpg" alt="Description">

<!-- Subdirectory -->
<img src="assets/image2.jpg" alt="Description">

<!-- ❌ WRONG - Don't duplicate directory name -->
<img src="my-project/image1.jpg" alt="Wrong">
```

### Image Galleries

**Single centered image:**
```html
<div class="image-container">
  <img src="large-photo.jpg" alt="Description">
</div>
```

**Two images side-by-side:**
```html
<div class="image-row">
  <img src="photo1.jpg" alt="Description 1">
  <img src="photo2.jpg" alt="Description 2">
</div>
```

**Multiple rows:**
```html
<div class="image-row">
  <img src="1.jpg" alt="Photo 1">
  <img src="2.jpg" alt="Photo 2">
</div>

<div class="image-row">
  <img src="3.jpg" alt="Photo 3">
  <img src="4.jpg" alt="Photo 4">
</div>
```

### YouTube Videos

**Responsive 16:9 embed** (padding-bottom: 56.25% technique):

```html
<div style="position: relative; padding-bottom: 56.25%; height: 0; overflow: hidden; max-width: 100%; margin: 20px 0;">
  <iframe
    src="https://www.youtube.com/embed/VIDEO_ID"
    style="position: absolute; top: 0; left: 0; width: 100%; height: 100%;"
    frameborder="0"
    allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture"
    allowfullscreen>
  </iframe>
</div>
```

**Extract video ID:**
- `https://youtu.be/dQw4w9WgXcQ` → `dQw4w9WgXcQ`
- `https://www.youtube.com/watch?v=dQw4w9WgXcQ` → `dQw4w9WgXcQ`

**MUST verify after adding:**
1. Deploy site
2. Check video thumbnail displays
3. Click play - video should load
4. Test responsive - resize browser window
5. Verify 16:9 aspect ratio maintained

### Downloading External Images

```bash
# Create directory if needed
mkdir -p docs/path/to/images

# Download image
curl -o docs/path/to/images/filename.jpg "https://example.com/image.jpg"

# Verify it's valid
file docs/path/to/images/filename.jpg
# Should output: JPEG image data, PNG image data, etc.

# Check file size (make sure it's not 0 bytes or HTML error page)
ls -lh docs/path/to/images/filename.jpg
```

**After downloading, MUST verify:**
1. Deploy site
2. Navigate to page with image
3. Confirm image displays correctly
4. Check aspect ratio maintained
5. Test on mobile breakpoint

---

## Common Issues & Fixes

### Issue: Images Cropped/Cut Off

**Cause:** Global CSS has `max-height` constraint

**Fix:** Remove `max-height` from global `img` selector

```css
/* Find this in docs/css/extra.css */
img {
    max-height: 400px;  /* ← Remove this line */
}

/* Replace with responsive approach */
img {
    max-width: 100%;
    height: auto;
}
```

**MUST verify:** Deploy and visually check images display at natural size.

### Issue: Images Stretched/Warped

**Cause:** Setting both `width` and `height` explicitly, or wrong aspect ratio

**Fix:** Use `height: auto` to maintain aspect ratio

```css
/* ❌ BAD */
img {
    width: 500px;
    height: 300px;  /* Forces aspect ratio, may stretch */
}

/* ✅ GOOD */
img {
    max-width: 500px;
    height: auto;  /* Maintains natural aspect ratio */
}
```

**MUST verify:** Deploy and visually confirm no distortion.

### Issue: Images Not Loading (404)

**Cause:** Incorrect relative path

**Fix:** Check path relative to markdown file location

```bash
# Find where markdown file is
ls -la docs/path/to/file.md

# Check where images are
ls -la docs/path/to/images/

# Adjust path in markdown accordingly
```

**MUST verify:**
1. Check browser Network tab - image returns 200 OK
2. Visually confirm image displays

### Issue: Horizontal Overflow on Mobile

**Cause:** Fixed widths or images exceeding viewport

**Fix:** Use `max-width: 100%` and test at 375px

```css
img {
    max-width: 100%;  /* Never exceed container */
}
```

**MUST verify:** Toggle DevTools responsive mode to 375px, scroll page - no horizontal scrollbar.

---

## Verification Checklist

### Browser-Based Verification (REQUIRED)

**Using Chrome DevTools MCP for every visual change:**

```python
# Navigate and screenshot
mcp__chrome-devtools__navigate_page(url="http://127.0.0.1:8000/page/")
mcp__chrome-devtools__take_screenshot()

# Verify all images
mcp__chrome-devtools__evaluate_script(function="""
() => {
  const images = document.querySelectorAll('img');
  return Array.from(images).map(img => ({
    src: img.src.split('/').pop(),
    displayed: img.offsetWidth + 'x' + img.offsetHeight,
    maxWidth: window.getComputedStyle(img).maxWidth
  }));
}
""")

# Check for layout issues
mcp__chrome-devtools__evaluate_script(function="""
() => {
  return {
    bodyWidth: document.body.scrollWidth,
    viewportWidth: window.innerWidth,
    hasHorizontalOverflow: document.body.scrollWidth > window.innerWidth
  };
}
""")
```

### Verification Checklist

For ANY visual changes, complete this checklist:

**Setup:**
- [ ] Site deployed (`zensical serve` running)
- [ ] Chrome DevTools MCP connected

**Visual Verification:**
- [ ] Screenshot taken of page
- [ ] Hero/header images constrained to expected width (e.g., 400px)
- [ ] Grid layouts show correct number of columns
- [ ] No horizontal overflow (scrollWidth <= viewportWidth)
- [ ] All images display (no broken image icons)

**Image-Specific Checks:**
- [ ] Verify displayWidth matches expected max-width
- [ ] Check naturalWidth vs displayWidth (image should be scaled down)
- [ ] Confirm aspect ratio preserved (not stretched)

---

## 📁 Project Structure

**Typical zensical/Material for MkDocs layout:**

```
project-root/
├── .venv/                   # Python virtual environment (REQUIRED, in .gitignore)
├── .github/                 # GitHub workflows (auto-generated by zensical new)
├── docs/                    # Content directory
│   ├── index.md             # Homepage
│   ├── stylesheets/         # Custom CSS (recommended location)
│   │   └── extra.css        # Custom styles
│   ├── javascripts/         # Custom JS (recommended location)
│   │   └── extra.js         # Custom scripts
│   ├── img/                 # Global images (logo, favicon, etc.)
│   │   ├── logo.png
│   │   └── favicon.ico
│   └── pages/               # Content pages
│       ├── page1.md
│       ├── page1/           # Page1 assets (images relative to markdown)
│       │   ├── image1.jpg
│       │   └── image2.jpg
│       └── page2.md
├── overrides/               # Theme template overrides (optional)
│   └── main.html            # Override main template
├── zensical.toml            # Site configuration (Zensical native)
├── mkdocs.yml               # Alternative config (Material for MkDocs)
├── .gitignore               # MUST include: .venv/, site/, .cache/
├── .cache/                  # Build cache (generated, in .gitignore)
└── site/                    # Built site output (generated, in .gitignore)
```

## 🛠️ Common Commands

```bash
# === Setup ===
# Create and activate virtual environment
python3 -m venv .venv
source .venv/bin/activate  # macOS/Linux
.venv\Scripts\activate     # Windows

# Install zensical
pip install zensical

# Bootstrap new project
zensical new .

# === Development ===
# Start dev server (auto-reloads on file changes)
zensical serve
# Site available at http://localhost:8000

# === Building ===
# Build static site
zensical build

# Build with strict error checking
zensical build --strict

# === Important Notes ===
# - Always activate .venv before running zensical commands
# - CSS changes require server restart (Ctrl+C, then zensical serve)
# - Built site is in site/ directory (ignored by git)
# - Development server has hot-reload for markdown/content
```

## 🎨 Customization & Theme

### Adding Custom CSS

**Location:** `docs/stylesheets/extra.css` (recommended)

**Configuration in zensical.toml:**
```toml
extra_css = ["stylesheets/extra.css"]
```

**Configuration in mkdocs.yml:**
```yaml
extra_css:
  - stylesheets/extra.css
```

### Adding Custom JavaScript

**Location:** `docs/javascripts/extra.js`

**Configuration in zensical.toml:**
```toml
extra_javascript = ["javascripts/extra.js"]
```

**Important:** Use the `document$` observable for initialization code to work with instant navigation:
```javascript
document$.subscribe(function() {
  // Your initialization code here
})
```

### Theme Customization with Template Overrides

**Setup custom_dir in zensical.toml:**
```toml
[project.theme]
custom_dir = "overrides"
```

**Override template blocks in `overrides/main.html`:**
```html
{% extends "base.html" %}

{% block htmltitle %}
  <title>Custom Title</title>
{% endblock %}

{% block footer %}
  {{ super() }}  <!-- Preserve original footer -->
  <div>Custom footer content</div>
{% endblock %}
```

**Available blocks:** `htmltitle`, `header`, `footer`, `scripts`, `styles`, and more.

**Template engine:** Zensical uses **MiniJinja** (Rust-based Jinja2 implementation).

### Theme Features & Configuration

**Common theme features in zensical.toml:**
```toml
[project.theme]
custom_dir = "overrides"
logo = "img/logo.png"
favicon = "img/favicon.ico"

features = [
  # Navigation
  "navigation.instant",         # Instant loading (SPA-like)
  "navigation.instant.prefetch", # Prefetch links on hover
  "navigation.tabs",            # Top-level tabs
  "navigation.tabs.sticky",     # Sticky navigation tabs
  "navigation.sections",        # Collapsible sections
  "navigation.indexes",         # Section index pages
  "navigation.top",             # Back to top button
  "navigation.tracking",        # URL updates with scroll
  "navigation.footer",          # Previous/next links in footer
  "navigation.path",            # Breadcrumb navigation

  # Search
  "search.highlight",           # Highlight search terms
  "search.suggest",             # Search suggestions

  # Header
  "header.autohide",            # Auto-hide header on scroll
  "announce.dismiss",           # Dismissible announcements

  # Code blocks
  "content.code.copy",          # Copy button for code
  "content.code.annotate",      # Code annotations
  "content.code.select",        # Select code block

  # Content features
  "content.tabs.link",          # Link content tabs
  "content.tooltips",           # Improved tooltips
  "content.footnote.tooltips",  # Footnote tooltips

  # TOC
  "toc.follow",                 # TOC follows scroll
  "toc.integrate",              # Integrate TOC into navigation
]

# Color scheme palettes
[[project.theme.palette]]
media = "(prefers-color-scheme: light)"
scheme = "default"
primary = "indigo"
accent = "indigo"

[[project.theme.palette]]
media = "(prefers-color-scheme: dark)"
scheme = "slate"
primary = "indigo"
accent = "indigo"
```

### Markdown Extensions

**Common extensions in zensical.toml:**
```toml
# Syntax highlighting
[project.markdown_extensions."pymdownx.highlight"]
anchor_linenums = true
line_spans = "__span"
pygments_lang_class = true

[project.markdown_extensions."pymdownx.inlinehilite"]
[project.markdown_extensions."pymdownx.snippets"]
[project.markdown_extensions."pymdownx.superfences"]

# Formatting
[project.markdown_extensions."pymdownx.caret"]   # ^^underline^^
[project.markdown_extensions."pymdownx.mark"]    # ==highlight==
[project.markdown_extensions."pymdownx.tilde"]   # ~~strikethrough~~

# Special features
[project.markdown_extensions."pymdownx.arithmatex"]
generic = true  # Math support with MathJax/KaTeX

[project.markdown_extensions."pymdownx.emoji"]
emoji_index = "zensical.extensions.emoji.twemoji"
emoji_generator = "zensical.extensions.emoji.to_svg"

[project.markdown_extensions."pymdownx.tasklist"]
custom_checkbox = true

[project.markdown_extensions.attr_list]
[project.markdown_extensions.md_in_html]
```

### Plugins

**Common plugins in zensical.toml:**
```toml
# Meta plugin (combines multiple plugins)
[project.plugins.meta]

# Git revision dates
[project.plugins."git-revision-date-localized"]
enable_creation_date = true
timezone = "America/Toronto"

# Table reader (CSV/Excel support)
[project.plugins."table-reader"]

# Search is built-in, no plugin needed
```

---

## 🚀 Deployment & Publishing

### Build for Production

```bash
# Activate virtual environment
source .venv/bin/activate

# Build static site
zensical build

# Build with strict validation
zensical build --strict

# Output is in site/ directory
```

**Built site characteristics:**
- Self-contained static files
- No database required
- No server-side processing needed
- Can be hosted on: GitHub Pages, CDN, static web server, S3, etc.
- Supports offline distribution

### GitHub Pages Deployment

**CRITICAL: Use GitHub Actions deploy-pages, NOT gh-pages branch**

Zensical projects should deploy using the official GitHub Pages Actions, not by pushing to a gh-pages branch.

**Correct workflow (`.github/workflows/ci.yml`):**

```yaml
name: Documentation
on:
  push:
    branches:
      - master
      - main

permissions:
  contents: read
  pages: write
  id-token: write

concurrency:
  group: "pages"
  cancel-in-progress: false

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v5
        with:
          fetch-depth: 0
      - uses: actions/setup-python@v5
        with:
          python-version: 3.x
      - name: Install dependencies
        run: pip install -r requirements.txt
      - name: Build site
        run: zensical build --clean
      - name: Upload artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: site

  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    needs: build
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```

**GitHub Pages Settings:**
- Go to repository Settings → Pages
- Source: **Deploy from a branch** should NOT be selected
- Instead: **GitHub Actions** should be the source
- Build type must be `workflow`, not `legacy`

**Verify/fix settings via CLI:**
```bash
# Check current settings
gh api repos/OWNER/REPO/pages | jq '{build_type, source}'

# Fix if needed (set to workflow mode)
gh api -X PUT repos/OWNER/REPO/pages --field build_type=workflow
```

**Common Issues:**
- If site shows wrong content, check that `build_type` is `workflow` not `legacy`
- If site shows README content, Pages is serving from wrong source
- Never use `gh-pages` branch deployment with zensical - always use Actions

**requirements.txt should contain:**
```
zensical
```

### Docker Deployment

Zensical provides an official Docker image at Docker Hub for containerized deployments.

```bash
# Pull official image
docker pull zensical/zensical

# Run in container
docker run -v $(pwd):/docs zensical/zensical build
```

---

## 📋 Quick Reference Checklists

### New Zensical Project Checklist

- [ ] Create virtual environment: `python3 -m venv .venv`
- [ ] Activate venv: `source .venv/bin/activate`
- [ ] Install zensical: `pip install zensical`
- [ ] Create project: `zensical new .`
- [ ] Verify `.gitignore` includes: `.venv/`, `site/`, `.cache/`
- [ ] Configure `zensical.toml` with `site_name` and `site_url`
- [ ] Test serve: `zensical serve`
- [ ] Visit http://localhost:8000 to verify

### Before Every Work Session Checklist

- [ ] Activate virtual environment: `source .venv/bin/activate`
- [ ] Pull latest changes: `git pull`
- [ ] Start dev server: `zensical serve`
- [ ] Verify site loads at http://localhost:8000

### Before Committing Changes Checklist

- [ ] Visual verification completed (if CSS/HTML/images changed)
- [ ] No console errors in DevTools
- [ ] Responsive breakpoints tested (375px, 768px, 1024px)
- [ ] All images load correctly (no 404s)
- [ ] Videos play correctly (if applicable)
- [ ] Build succeeds: `zensical build --strict`
- [ ] `.venv/`, `site/`, `.cache/` in `.gitignore`

---

## 🔗 Additional Resources

### Official Documentation
- **Zensical Docs:** https://zensical.org/docs/
- **Getting Started:** https://zensical.org/docs/get-started/
- **Create Your Site:** https://zensical.org/docs/create-your-site/
- **Customization:** https://zensical.org/docs/customization/

### Related Tools
- **Material for MkDocs:** https://squidfunk.github.io/mkdocs-material/
- **MkDocs:** https://www.mkdocs.org/
- **Python venv:** https://docs.python.org/3/library/venv.html
- **uv Package Manager:** https://github.com/astral-sh/uv

### Key Concepts
- **Zensical = Material for MkDocs** (same ecosystem, Rust-powered)
- **Static site generator** (no backend, no database)
- **Markdown-based** (write content in .md files)
- **Python-based** (requires Python + venv)
- **TOML configuration** (zensical.toml) or YAML (mkdocs.yml)

---

## Git Commits & Attributions

**CRITICAL: No Claude Attributions**

When working on Zensical projects, NEVER add Claude or AI attributions to:
- Commit messages
- Pull request descriptions
- Code comments
- Documentation
- Any generated content

**Commit messages should be:**
- Clean and professional
- Focused on the changes made
- Free of AI/Claude signatures, co-author tags, or "Generated with Claude Code" footers

**Example - DO NOT:**
```
🤖 Generated with Claude Code

Co-Authored-By: Claude <noreply@anthropic.com>
```

**Example - DO:**
```
Add responsive image gallery to homepage
```

---

## Summary

**Remember the Golden Rules:**
1. ✅ Every Zensical project MUST have `.venv/`
2. ✅ `.gitignore` MUST include: `.venv/`, `site/`, `.cache/`
3. ✅ Always activate venv before running zensical commands
4. ✅ Visual verification is MANDATORY for CSS/HTML/image changes
5. ✅ Test responsive breakpoints (375px, 768px, 1024px)
6. ✅ CSS changes require server restart
7. ✅ Images use `max-width: 100%; height: auto;` for responsive design
8. ✅ Use `zensical serve` for development, `zensical build` for production
9. ✅ NEVER add Claude/AI attributions to commits, PRs, or code

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/zeulewan) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
