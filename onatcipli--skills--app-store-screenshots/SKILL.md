---
name: app-store-screenshots
description: > Use when this capability is needed.
metadata:
  author: onatcipli
---

# App Store Screenshots

Research competitor screenshots, plan your strategy, build production-ready
screenshots, and upload them to App Store Connect.

## Overview

Screenshots are critical for App Store conversion—90% of users don't scroll past
the third screenshot. This skill covers the full pipeline from competitor research
through final upload.

## Important: Correct Screenshot Dimensions

```
iPhone 6.9" (Required): 1260 x 2736   ← iPhone 16 Pro Max
iPhone 6.7":             1290 x 2796   ← iPhone 16 Plus, 15 Pro Max
iPad 13" (Required):     2064 x 2752   ← iPad Pro M4

WARNING: Many online sources (including older versions of this skill) incorrectly
list 1290x2796 or 1320x2868 as the 6.9" dimension. The correct 6.9" dimension
is 1260 x 2736. Verify at: https://developer.apple.com/help/app-store-connect/reference/screenshot-specifications/
```

---

## Workflow 0 — Onboarding Interview

Before starting, gather information about the user's app and goals.

### Questions to Ask

```
1. Is this a new app or an existing app?
   □ New app (focus on competitor research)
   □ Existing app (can also analyze current performance)

2. What is your app's category?
   [Select from App Store categories - see category-codes in references]

3. Describe your app in one sentence:
   [Used to extract keywords for competitor search]

4. What is your app name?
   [Used for folder organization]

5. What are your main competitors? (optional)
   [If known, provide app names or IDs]

6. Do you have Sensor Tower API access?
   □ Yes (enhanced competitor finding)
   □ No (use iTunes API + RSS feeds only)

7. What locales do you need screenshots for?
   [e.g., en-US, tr, de-DE, ja]
```

### Output

Create project folder structure:

```
{app_name}_screenshots/
├── competitors/
├── analysis/
├── your_app/
│   ├── iphone/
│   ├── ipad/
│   └── assets/
├── preview/
├── export/
│   └── locales/
│       ├── en-US/
│       └── {other locales}/
└── README.md
```

---

## Workflow 1 — Find Competitors

Identify the top competitors in your category to analyze.

### Step 1: Search by Keywords

Extract keywords from app description and search iTunes:

```
GET https://itunes.apple.com/search?term={keywords}&entity=software&country=us&limit=25
```

### Step 2: Fetch Top Charts

Get top apps in the category via RSS feed:

```
GET https://rss.applemarketingtools.com/api/v2/us/apps/top-free/50/apps.json?genre={genreId}
```

### Step 3: Filter & Rank (Optional with Sensor Tower)

If Sensor Tower API available:
```
GET https://app.sensortower.com/api/ios/apps?app_ids={ids}
```

Use download/revenue estimates to find "winning" apps.

### Step 4: Select Top 3 Competitors

Present candidates to user:

```markdown
## Competitor Candidates

| # | App Name | Rating | Reviews | Category Rank |
|---|----------|--------|---------|---------------|
| 1 | Calm | 4.8★ | 1.2M | #1 Health |
| 2 | Headspace | 4.9★ | 850K | #2 Health |
| 3 | Insight Timer | 4.9★ | 450K | #5 Health |

Select 3 competitors to analyze (enter numbers): [1, 2, 3]
```

---

## Workflow 2 — Download & Analyze Screenshots

Download competitor screenshots and generate analysis.

### Step 1: Fetch Screenshot URLs

For each competitor:

```
GET https://itunes.apple.com/lookup?id={appId}&country=us
```

Extract from response:
- `screenshotUrls` — iPhone screenshots
- `ipadScreenshotUrls` — iPad screenshots

### IMPORTANT: Fallback for Empty Screenshot Arrays

Many apps return empty `screenshotUrls: []` from the iTunes API. This is common
and not a rate limit issue—the API simply doesn't include screenshots for some apps.

**Fallback strategy (try in order):**

1. **Try different country codes:** `us`, `gb`, `de`, `cy`, `tr`, `au`
   ```
   GET https://itunes.apple.com/lookup?id={appId}&country=gb
   ```

2. **Scrape the App Store web page HTML:**
   App Store pages render client-side via JavaScript, but the raw HTML source
   often contains mzstatic.com screenshot URLs embedded as data attributes.
   ```
   Fetch: https://apps.apple.com/us/app/id{appId}
   Search HTML source for: mzstatic.com URLs containing screenshot patterns
   Filter for URLs with dimensions in the path (e.g., /1242x2208bb.png)
   ```

3. **Manual capture:** As a last resort, instruct the user to screenshot the
   App Store listing on their device or visit the web page in a browser.

### Step 2: Download Screenshots

```python
import requests
import os
import json
import subprocess
from pathlib import Path

def download_screenshots(app_id, app_name, output_dir, country="us"):
    # Fetch app data — try multiple countries if needed
    countries = [country, "us", "gb", "de", "au", "cy"]
    data = None

    for cc in countries:
        response = requests.get(f"https://itunes.apple.com/lookup?id={app_id}&country={cc}")
        result = response.json()
        if result["resultCount"] > 0:
            app = result["results"][0]
            if app.get("screenshotUrls"):
                data = app
                break

    if not data:
        print(f"WARNING: No screenshots found for {app_name} via iTunes API")
        print("Try scraping the App Store web page HTML for mzstatic.com URLs")
        return None

    # Create directories
    base_path = Path(output_dir) / "competitors" / app_name
    iphone_path = base_path / "iphone"
    ipad_path = base_path / "ipad"
    iphone_path.mkdir(parents=True, exist_ok=True)
    ipad_path.mkdir(parents=True, exist_ok=True)

    # Download and verify dimensions
    screenshots_meta = []
    for i, url in enumerate(data.get("screenshotUrls", []), 1):
        filepath = iphone_path / f"{i:02d}.png"
        img = requests.get(url)
        with open(filepath, "wb") as f:
            f.write(img.content)

        # Verify actual dimensions (CDN may return smaller than requested)
        dims = get_image_dimensions(filepath)
        screenshots_meta.append({
            "index": i,
            "url": url,
            "actualWidth": dims[0],
            "actualHeight": dims[1]
        })

    # Save enriched metadata
    metadata = {
        **data,
        "downloadDate": str(date.today()),
        "screenshotCount": len(data.get("screenshotUrls", [])),
        "ipadScreenshotCount": len(data.get("ipadScreenshotUrls", [])),
        "screenshotDetails": screenshots_meta
    }
    with open(base_path / "metadata.json", "w") as f:
        json.dump(metadata, f, indent=2)

def get_image_dimensions(filepath):
    """Get actual pixel dimensions. CDN URLs don't upscale."""
    # macOS: sips -g pixelWidth -g pixelHeight {file}
    # Linux: identify {file} (ImageMagick) or python Pillow
    from PIL import Image
    img = Image.open(filepath)
    return img.size
```

### CDN Dimension Warning

**The CDN does NOT upscale images.** If a developer uploaded 5.5" screenshots
(1242x2208), requesting a URL ending in `1320x2868bb.png` silently returns the
original 1242x2208 image. Always verify actual dimensions after downloading.

### Step 3: Analyze Each Competitor

For each competitor, analyze and document:

```markdown
## Screenshot Analysis: {App Name}

### Overview
- **iPhone Screenshots:** {count}
- **iPad Screenshots:** {count}
- **Actual Dimensions:** {width}x{height} (may differ from URL)
- **Visual Style:** [Device mockup / Lifestyle / UI-only / Hybrid]
- **Color Palette:** [Primary colors observed]

### Screenshot Breakdown

| # | Type | Caption/Text | Feature Shown | Notes |
|---|------|--------------|---------------|-------|
| 1 | {type} | "{caption}" | {feature} | {notes} |
| 2 | {type} | "{caption}" | {feature} | {notes} |
...

### Strategy Analysis
- **Hook (Screenshot 1):** {description}
- **Flow:** {how screenshots progress}
- **Text Style:** {short/long, benefit/feature focused}
- **Device Frames:** {yes/no, style}
- **ASO Keywords in Captions:** {observed keywords}

### Strengths
- {strength 1}
- {strength 2}

### Weaknesses
- {weakness 1}
- {weakness 2}
```

Save as `competitors/{app_name}/README.md`

---

## Workflow 3 — Compare & Identify Patterns

Generate a cross-competitor analysis.

### Comparison Table

```markdown
## Competitor Comparison

### Visual Style

| Aspect | Competitor 1 | Competitor 2 | Competitor 3 |
|--------|--------------|--------------|--------------|
| Screenshot Count | 8 | 6 | 10 |
| Style | Device mockup | Lifestyle | Hybrid |
| Text Amount | Minimal | Heavy | Medium |
| Color Theme | Blue/Purple | Green/White | Orange/Black |
| Device Frames | Yes | No | Yes |
| Frame Type | Dynamic Island | N/A | Dynamic Island |

### Caption Patterns

| Screenshot | Competitor 1 | Competitor 2 | Competitor 3 |
|------------|--------------|--------------|--------------|
| 1 (Hook) | "Find Peace" | "Sleep Better" | "Meditate Daily" |
| 2 | "Sleep Stories" | "Guided Sessions" | "Track Progress" |
| 3 | "Daily Calm" | "Reduce Stress" | "Join Community" |

### ASO Keywords in Screenshots
| Keyword | Comp 1 | Comp 2 | Comp 3 | Search Volume |
|---------|--------|--------|--------|---------------|
| "calorie" | ✓ | ✓ | ✗ | High |
| "tracker" | ✓ | ✗ | ✓ | High |
| "AI" | ✗ | ✓ | ✓ | Medium |

### Common Patterns
1. {pattern 1}
2. {pattern 2}
3. {pattern 3}

### Differentiation Opportunities
1. {opportunity 1}
2. {opportunity 2}
```

Save as `analysis/competitor_comparison.md`

---

## Workflow 4 — ASO Keyword Research for Captions

**Do this BEFORE writing captions.** Screenshot captions are now indexed by Apple's
search algorithm (as of June 2025), making keyword placement critical.

### Step 1: Research Category Keywords

```
GET https://itunes.apple.com/search?term={category}&entity=software&country=us&limit=25
```

Extract keyword patterns from competitor app names and subtitles:
```
"Cal AI - Calorie Tracker" → keywords: cal, ai, calorie, tracker
"Lifesum: AI Calorie Counter" → keywords: ai, calorie, counter
"YAZIO – Calorie Counter & Diet" → keywords: calorie, counter, diet
```

### Step 2: Identify High-Value Keywords

From competitor analysis, rank keywords by:
- Frequency in competitor names (more = validated)
- Search volume (estimate from App Store suggestions)
- Relevance to your app

### Step 3: Map Keywords to Screenshots

Each screenshot caption should contain at least one target keyword:

```markdown
## Keyword-Optimized Screenshot Captions

Target keywords: calorie, tracker, AI, scan, food, diet

| # | Before (generic) | After (ASO-optimized) | Keywords |
|---|-------------------|-----------------------|----------|
| 1 | "Snap It. Track It." | "Scan Food, Count Calories" | scan, food, calories |
| 2 | "See Your Progress" | "AI Calorie Tracker" | AI, calorie, tracker |
| 3 | "Eat Smarter" | "Smart Diet Insights" | diet, insights |
```

### Step 4: Localize Keywords per Market

Different markets search differently:

```markdown
| Locale | Top Keywords |
|--------|--------------|
| en-US | calorie tracker, food scanner, diet app |
| tr | kalori sayacı, yemek takip, diyet uygulaması |
| de-DE | kalorienzähler, ernährung, diät tracker |
| ja | カロリー計算, 食事管理, ダイエット |
```

---

## Workflow 5 — Plan Your Screenshots

Based on analysis and keyword research, create a screenshot plan.

### Screenshot Strategy Framework

```
The "Value-Usage-Trust" Formula:
1. VALUE — What the user gets (hook) + primary keyword
2. USAGE — How it works (key features) + feature keywords
3. TRUST — Social proof (ratings, awards)
```

### Generate Screenshot Plan

```markdown
## Screenshot Plan: {Your App Name}

### Strategy
- **Style:** {chosen style based on analysis}
- **Count:** {recommended: 6-8}
- **Text Approach:** {based on competitor analysis}
- **Differentiator:** {what makes yours unique}
- **Target Keywords:** {from Workflow 4}

### Screenshot Sequence

#### Screenshot 1: Hook / Value Proposition
- **Caption:** "{ASO-optimized caption}"
- **Subtitle:** "{supporting text}"
- **Visual:** {description of what to show}
- **Keywords:** {keywords included}
- **Goal:** Grab attention, communicate core value

#### Screenshot 2: Key Feature #1
...

### Design Guidelines
- **Colors:** {recommended palette}
- **Font:** {recommendation}
- **Device Frame:** Use Dynamic Island style (not notch)
- **Safe Zones:** Keep text 120px from edges, 160px from top
- **Caption Font Size:** 140-160px title, 48-56px subtitle
- **Letter Spacing:** -4px to -5px for bold impact

### Export Dimensions
- [ ] iPhone 6.9": 1260 x 2736 (iPhone 16 Pro Max)
- [ ] iPad 13": 2064 x 2752
- [ ] PNG format, no transparency
- [ ] Under 8 MB each
```

Save as `your_app/plan.md`

---

## Workflow 6 — Build Screenshots (HTML/CSS Export Tool)

Create production screenshots using an HTML/CSS builder.

### Step 1: Generate export-tool.html

Create a self-contained HTML file that renders each screenshot at exact pixel
dimensions. See [templates/export-tool.html](templates/export-tool.html) for the
full template.

Key components:
- Screenshot canvas divs at exact target dimensions (1260x2736)
- CSS `transform: scale()` for in-browser preview display
- html2canvas.js for PNG export
- Per-screenshot customization (background, text, assets)

### Step 2: Phone Frame Component

Use a modern Dynamic Island frame (not the outdated notch):

```css
.phone-frame {
    position: relative;
    width: 380px;
    height: 780px;
    border-radius: 55px;
    border: 8px solid #8a8a8f; /* Titanium color */
    background: #000;
    box-shadow:
        inset 0 0 0 3px #1a1a1a,
        6px 0 0 -2px #6b6b6f,    /* Power button */
        -6px 40px 0 -2px #6b6b6f, /* Volume up */
        -6px 90px 0 -2px #6b6b6f; /* Volume down */
    overflow: hidden;
}

.dynamic-island {
    position: absolute;
    top: 16px;
    left: 50%;
    transform: translateX(-50%);
    width: 130px;
    height: 40px;
    background: #000;
    border-radius: 22px;
    z-index: 10;
}
```

### Step 3: Typography System

Standardized caption layout for consistency:

```css
.screenshot-title {
    font-size: 140px;
    font-weight: 900;
    letter-spacing: -4px;
    line-height: 1.05;
    text-align: center;
}

.screenshot-subtitle {
    font-size: 52px;
    font-weight: 500;
    letter-spacing: -1px;
    opacity: 0.85;
    text-align: center;
}
```

### Step 4: Asset Integration

Guide for incorporating app assets:
- App icon (PNG, transparent background)
- Mascot/character images
- UI screenshots from actual device
- Position using absolute positioning with overflow for dramatic effect

### CRITICAL: Serving the Export Tool

```
⚠️ NEVER open export-tool.html as a file:// URL.
   Canvas export will fail with: "Tainted canvases may not be exported"

✅ ALWAYS serve via HTTP:
   python3 -m http.server 8080
   Then visit: http://localhost:8080/export-tool.html

✅ Add crossorigin="anonymous" to ALL <img> tags
✅ In html2canvas config, use: { useCORS: true }
```

---

## Workflow 7 — Export Screenshots

Two methods for exporting at correct dimensions.

### Method A: Browser Export (html2canvas)

Include html2canvas via CDN in your export tool:

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/html2canvas/1.4.1/html2canvas.min.js"></script>

<script>
async function exportScreenshot(elementId, filename) {
    const element = document.getElementById(elementId);
    const canvas = await html2canvas(element, {
        width: 1260,
        height: 2736,
        scale: 1,
        useCORS: true,
        logging: false
    });

    const link = document.createElement('a');
    link.download = filename;
    link.href = canvas.toDataURL('image/png');
    link.click();
}
</script>
```

### Method B: Puppeteer Headless Capture (Recommended for Automation)

Puppeteer produces more reliable output for fonts and images.

```javascript
const puppeteer = require('puppeteer');

async function captureScreenshot(url, elementId, outputPath) {
    const browser = await puppeteer.launch();
    const page = await browser.newPage();

    // Set viewport to exact screenshot dimensions
    await page.setViewport({ width: 1260, height: 2736 });
    await page.goto(url, { waitUntil: 'networkidle0' });

    // Clone element into a fixed full-size wrapper
    // (direct capture fails because of CSS transform: scale)
    await page.evaluate((id) => {
        // Hide all existing page content
        for (const child of document.body.children) {
            child.style.display = 'none';
        }

        // Create fixed wrapper at exact viewport size
        const wrapper = document.createElement('div');
        wrapper.style.cssText = `
            position: fixed; top: 0; left: 0;
            width: 1260px; height: 2736px;
            overflow: hidden; z-index: 99999;
        `;

        // Clone the target element at full size (no transform)
        const original = document.getElementById(id);
        const clone = original.cloneNode(true);
        clone.style.cssText = `
            transform: none;
            width: 1260px; height: 2736px;
            position: absolute; top: 0; left: 0;
        `;

        wrapper.appendChild(clone);
        document.body.prepend(wrapper);
    }, elementId);

    await page.screenshot({
        path: outputPath,
        clip: { x: 0, y: 0, width: 1260, height: 2736 }
    });

    await browser.close();
}
```

### Verify Exported Dimensions

Always verify after export:

```bash
# macOS
sips -g pixelWidth -g pixelHeight screenshot.png

# Linux (ImageMagick)
identify screenshot.png

# Expected: 1260 x 2736 (iPhone 6.9")
```

### Export Naming Convention

```
{nn}_{name}_{locale}.png

Examples:
01_hook_en-US.png
02_scan_food_en-US.png
03_ai_tracker_en-US.png
01_hook_tr.png
02_yemek_tara_tr.png
```

---

## Workflow 8 — Localize Screenshots

Create localized versions for multiple App Store locales.

### Step 1: Template System

Design screenshots with swappable text layers:
- Same visual layout across all locales
- Only text/captions change per locale
- Keep visual assets (device frames, UI, mascot) identical

### Step 2: Translate Captions (Don't Just Translate—Localize)

Use ASO-optimized keywords per locale (from Workflow 4):

```markdown
| Screenshot | en-US | tr | de-DE |
|------------|-------|-----|-------|
| 1 | "Scan Food, Count Calories" | "Yemeği Tara, Kalori Say" | "Essen Scannen, Kalorien Zählen" |
| 2 | "AI Calorie Tracker" | "AI Kalori Takibi" | "KI Kalorienzähler" |
| 3 | "Smart Diet Insights" | "Akıllı Diyet Analizi" | "Smarte Diät-Einblicke" |
```

### Step 3: Export Per Locale

```
export/
└── locales/
    ├── en-US/
    │   ├── 01_hook.png
    │   ├── 02_feature1.png
    │   └── ...
    ├── tr/
    │   ├── 01_hook.png
    │   └── ...
    └── de-DE/
        ├── 01_hook.png
        └── ...
```

### Step 4: Side-by-Side Review

Preview all locales together before uploading to verify consistency.

---

## Workflow 9 — Upload to App Store Connect

Upload screenshots programmatically via the App Store Connect API.

### Prerequisites

- App Store Connect API key (.p8 file)
- Key ID and Issuer ID
- App already created in App Store Connect
- An editable version (PREPARE_FOR_SUBMISSION state)

### JWT Authentication

```javascript
const jwt = require('jsonwebtoken');
const fs = require('fs');

const privateKey = fs.readFileSync('AuthKey_{keyId}.p8');
const token = jwt.sign(
    {
        iss: ISSUER_ID,
        iat: Math.floor(Date.now() / 1000),
        exp: Math.floor(Date.now() / 1000) + 1200,
        aud: 'appstoreconnect-v1'
    },
    privateKey,
    { algorithm: 'ES256', header: { alg: 'ES256', kid: KEY_ID, typ: 'JWT' } }
);
```

### API Flow

1. **Find app:**
   ```
   GET /v1/apps?filter[bundleId]={bundleId}
   ```

2. **Find editable version:**
   ```
   GET /v1/apps/{id}/appStoreVersions?filter[appStoreState]=PREPARE_FOR_SUBMISSION
   ```

   Valid filter states: `PREPARE_FOR_SUBMISSION`, `DEVELOPER_REJECTED`,
   `REJECTED`, `METADATA_REJECTED`, `WAITING_FOR_REVIEW`, `IN_REVIEW`,
   `PENDING_DEVELOPER_RELEASE`, `READY_FOR_SALE`

   **WARNING:** `READY_FOR_DISTRIBUTION` is NOT a valid state value.

3. **Get/create localizations:**
   ```
   GET /v1/appStoreVersions/{id}/appStoreVersionLocalizations
   ```

4. **Create screenshot set:**
   ```
   POST /v1/appScreenshotSets
   ```
   Body:
   ```json
   {
     "data": {
       "type": "appScreenshotSets",
       "attributes": {
         "screenshotDisplayType": "APP_IPHONE_67"
       },
       "relationships": {
         "appStoreVersionLocalization": {
           "data": { "type": "appStoreVersionLocalizations", "id": "{locId}" }
         }
       }
     }
   }
   ```

5. **Delete existing screenshots** (if replacing):
   ```
   DELETE /v1/appScreenshots/{screenshotId}
   ```

6. **For each screenshot:**
   ```
   a. Reserve: POST /v1/appScreenshots
      { fileName, fileSize, sourceFileChecksum (MD5) }

   b. Upload binary to the URL(s) provided in response

   c. Commit: PATCH /v1/appScreenshots/{id}
      { uploaded: true, sourceFileChecksum: MD5 }
   ```

### Display Type Mapping

```
IMPORTANT: APP_IPHONE_69 does NOT exist in the API.
iPhone 6.9" screenshots map to the 6.7" display type.

| Device          | API Display Type          | Accepted Dimensions |
|-----------------|--------------------------|---------------------|
| iPhone 6.9"     | APP_IPHONE_67            | 1260 x 2736         |
| iPhone 6.7"     | APP_IPHONE_67            | 1290 x 2796         |
| iPhone 6.5"     | APP_IPHONE_65            | 1284 x 2778         |
| iPhone 6.1"     | APP_IPHONE_61            | 1179 x 2556         |
| iPad 13"        | APP_IPAD_PRO_3GEN_129    | 2064 x 2752         |
| iPad 12.9"      | APP_IPAD_PRO_3GEN_129    | 2048 x 2732         |
```

See [asc-upload-api.md](references/asc-upload-api.md) for full details.

---

## Quick Reference — APIs

| Task | Endpoint |
|------|----------|
| Search apps | `itunes.apple.com/search?term={q}&entity=software` |
| Lookup app | `itunes.apple.com/lookup?id={id}&country={cc}` |
| Top Free | `rss.applemarketingtools.com/api/v2/{cc}/apps/top-free/{limit}/apps.json` |
| Top Paid | `rss.applemarketingtools.com/api/v2/{cc}/apps/top-paid/{limit}/apps.json` |
| By Category | Add `?genre={genreId}` to RSS feeds |
| ASC: Screenshot sets | `POST /v1/appScreenshotSets` |
| ASC: Upload screenshot | `POST /v1/appScreenshots` |

---

## Folder Structure

```
{app_name}_screenshots/
├── competitors/
│   ├── {competitor_1}/
│   │   ├── iphone/
│   │   │   ├── 01.png
│   │   │   └── ...
│   │   ├── ipad/
│   │   │   └── ...
│   │   ├── metadata.json
│   │   └── README.md
│   ├── {competitor_2}/
│   └── {competitor_3}/
├── analysis/
│   ├── competitor_comparison.md
│   └── patterns.md
├── your_app/
│   ├── plan.md
│   ├── iphone/
│   │   ├── 01_hook.png
│   │   └── ...
│   ├── ipad/
│   │   └── ...
│   └── assets/
│       ├── ui_screenshots/
│       └── photos/
├── preview/
│   └── index.html
├── export/
│   ├── export-tool.html
│   ├── capture.js
│   └── locales/
│       ├── en-US/
│       └── {other locales}/
└── README.md
```

---

## Reference Files

| File | Purpose |
|------|---------|
| [screenshot-specs.md](references/screenshot-specs.md) | All device sizes and technical requirements |
| [analysis-templates.md](references/analysis-templates.md) | Templates for competitor analysis |
| [itunes-api.md](references/itunes-api.md) | API endpoints + fallback strategies |
| [asc-upload-api.md](references/asc-upload-api.md) | App Store Connect upload API |
| [export-pipeline.md](references/export-pipeline.md) | html2canvas + Puppeteer export guide |
| [preview-website.html](templates/preview-website.html) | Preview tool template |
| [export-tool.html](templates/export-tool.html) | Screenshot builder template |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/onatcipli) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
