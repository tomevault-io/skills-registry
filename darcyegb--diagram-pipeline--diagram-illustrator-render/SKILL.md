---
name: diagram-illustrator-render
description: > Use when this capability is needed.
metadata:
  author: darcyegb
---

# Skill 4: diagram-illustrator-render

**Pipeline Position:** Skill 4 of 4
**Upstream Dependencies:** diagram-visual-encoding (Skill 2), diagram-graphic-design (Skill 3)
**Output:** Adobe Illustrator file (.ai), rendered PNG (200% scale), optionally PDF for print
**Execution Context:** AppleScript + ExtendScript via osascript on macOS

---

## Input

This skill consumes two structured specifications:

### From diagram-graphic-design (Skill 3)
A YAML design specification defining the visual system:

```yaml
grid:
  artboard_width: 800
  artboard_height: 600
  base_unit: 8
  margins: {top: 24, right: 24, bottom: 24, left: 24}
  gutter: 16

type:
  primary_font: "Helvetica Neue"
  secondary_font: "Menlo"
  scale:
    display: {size: 48, weight: 700, tracking: -1, leading: 56}
    headline: {size: 28, weight: 600, tracking: 0, leading: 36}
    body: {size: 14, weight: 400, tracking: 0, leading: 22}
    caption: {size: 11, weight: 400, tracking: 0.5, leading: 16}

color:
  primary: [66, 133, 244]      # RGB
  surface: [248, 249, 250]
  text_primary: [32, 33, 36]
  text_secondary: [95, 99, 104]
  divider: [218, 220, 224]

elements:
  stroke:
    heavy: 3
    medium: 2
    light: 1
  corners: 4
  spacing:
    xs: 8
    sm: 12
    md: 16
    lg: 24
    xl: 32

composition: "two_column" | "single_flow" | "radial" | "matrix"
```

### From diagram-visual-encoding (Skill 2)
A visual encoding plan defining what goes where:

```yaml
composition_type: "two_column"
channels:
  position: "x_y_grid"
  size: "element_hierarchy"
  color: "category_mapped"

layout:
  left_column: {width_units: 4, contains: ["labels", "legend"]}
  right_column: {width_units: 4, contains: ["diagram", "data_viz"]}

spatial_plan:
  - id: "title"
    position: [0, 0]
    size: {units_w: 8, units_h: 2}
  - id: "content_area"
    position: [0, 2]
    size: {units_w: 8, units_h: 4}
```

The skill reads only what's needed: grid dimensions, type definitions, color values, element stroke/corner specs, composition type. It ignores design rationale and selection criteria.

---

## Architecture

The skill executes in a precise AppleScript ↔ ExtendScript cycle. This sequence prevents stalls and ensures reliable file I/O.

### Execution Loop

```
Write JSX to /sessions/vibrant-peaceful-allen/my_diagram.jsx
           ↓
cp JSX to /mnt/<user>/Desktop/my_diagram.jsx (sync to Mac)
           ↓
osascript: activate Illustrator + delay 1 + do javascript file
           ↓
Read exported PNG with Read tool
           ↓
Evaluate against spec (grid alignment, type hierarchy, color, composition)
           ↓
Fix issues in JSX (if needed)
           ↓
Re-execute (repeat until specification met)
           ↓
Final PNG + optional .ai file saved
```

### JSX File Execution Pattern (Critical)

The correct pattern prevents -1700 "event not handled" errors and AppleScript stalls:

**CORRECT:**
```applescript
set jsxPath to POSIX file "/mnt/<user>/Desktop/my_diagram.jsx"
do javascript file (jsxPath as text)
```

**INCORRECT (causes -1700 error):**
```applescript
do javascript file "/mnt/<user>/Desktop/my_diagram.jsx"  -- ✗ Doesn't work
```

**Why:** `POSIX file` is required to create an AppleScript file object. The `as text` coercion converts that to the string path Illustrator's JavaScript handler expects. Other patterns skip the object step and fail.

**Full AppleScript template:**
```applescript
tell application "Adobe Illustrator"
    activate
    delay 1
    set jsxPath to POSIX file "/mnt/<user>/Desktop/my_diagram.jsx"
    do javascript file (jsxPath as text)
end tell
```

### Document Cleanup (Before Every Run)

Execute this in AppleScript before each JSX run to prevent MRAP errors from stale documents:

```applescript
tell application "Adobe Illustrator"
    do javascript "while(app.documents.length>0){app.documents[0].close(SaveOptions.DONOTSAVECHANGES);}"
end tell

delay 2  -- Wait for cleanup to finish
```

Then proceed with the JSX execution pattern above. This closes all unsaved documents and prevents "Application is busy" errors.

### File Paths and Sync

All work paths on the Linux session:
- JSX written to: `/sessions/vibrant-peaceful-allen/my_diagram.jsx`
- Synced to Mac: `/mnt/<username>/Desktop/my_diagram.jsx`
- PNG exported by Illustrator to: `/mnt/<username>/Desktop/diagram_preview.png`
- Read back via tool: `/mnt/<username>/Desktop/diagram_preview.png`

The `/mnt` mount point is the user's local Mac filesystem. This allows Illustrator (running on Mac) to access and export files visible to the Linux session.

---

## Core Workflow

### Phase 1: Translate Specification to Script Structure

Map the design spec YAML to JSX source code structure:

**Grid → Artboard + Position Math**
```
artboard_width: 800, artboard_height: 600, base_unit: 8
→ JSX artboard dimensions (800 × 600 pt)
→ all element positions = (grid_col × base_unit, grid_row × base_unit)
→ margins, gutters calculated as unit multiples (24pt = 3 units, etc.)
```

**Type → Font Constants**
```
headline: {size: 28, weight: 600}
→ JSX: var headline = {font: "Helvetica Neue", size: 28, weight: 600};
→ ApplyFont("Helvetica Neue", 28, 600) helper function
```

**Color → RGB Helper**
```
primary: [66, 133, 244]
→ JSX: var colors = {primary: [66, 133, 244], surface: [248, 249, 250]};
→ rgb(66, 133, 244) → return new RGBColor() with R:66, G:133, B:244
```

**Elements → Stroke/Corner Specs**
```
stroke.medium: 2, corners: 4
→ JSX: const strokeWeight = 2;
→ var cornerRadius = 4;
→ Use in rect().strokeWeight = strokeWeight
```

**Composition Type → Drawing Function Order**
```
composition: "two_column"
→ JSX: use two_column_layout() function from references/composition-implementations.md
→ executes left column shapes, then right column shapes (front-to-back)
```

### Coordinate System Translation (Critical)

**Illustrator Origin:** Bottom-left corner of artboard. Y-axis increases upward.
**Grid Specification Origin:** Top-left corner (conventional web/print origin). Y increases downward.

**Conversion:**
```
Design spec (top-left origin):
  "place title 24pt from top" = y: 24

Illustrator (bottom-left origin):
  artboard_height = 600
  illustrator_y = 600 - 24 = 576

rectangle(top, left, width, height) in JSX:
  - top param = Y coordinate of top edge (in Illustrator coords)
  - For design spec y=24 from top: top = 600 - 24 = 576
  - left param = X coordinate of left edge
  - width, height = dimensions

rectangle(576, 24, 400, 60) creates a rect with:
  - Top edge at Y=576 (24pt from top of 600pt artboard)
  - Left edge at X=24 (24pt from left)
  - 400pt wide, 60pt tall
```

Apply this conversion consistently to all `y` coordinates from the design spec.

### Phase 2: Build the JSX Script

Structure the JSX in this order. This ensures correct z-order (back-to-front rendering):

**1. Close Existing Documents & Create New**
```jsx
while(app.documents.length > 0) {
    app.documents[0].close(SaveOptions.DONOTSAVECHANGES);
}

var doc = app.documents.add(DocumentColorSpace.RGB, 800, 600);
doc.rulerUnits = Units.POINTS;
```

**2. Define Helper Functions**
```jsx
function rgb(r, g, b) {
    var color = new RGBColor();
    color.red = r;
    color.green = g;
    color.blue = b;
    return color;
}

function rectangle(top, left, width, height, fillColor, strokeColor, strokeWeight) {
    var rect = doc.pathItems.rectangle(top, left, width, height);
    if (fillColor) {
        rect.filled = true;
        rect.fillColor = fillColor;
    }
    if (strokeColor) {
        rect.stroked = true;
        rect.strokeColor = strokeColor;
        rect.strokeWidth = strokeWeight || 1;
    }
    return rect;
}

function textFrame(text, x, y, width, height, fontSize, fontName, fontColor) {
    var txt = doc.textFrames.add();
    txt.contents = text;
    txt.left = x;
    txt.top = y;
    txt.width = width;
    txt.height = height;
    txt.textRange.characterAttributes.size = fontSize;
    txt.textRange.characterAttributes.textFont = app.fonts.getByName(fontName);
    txt.textRange.characterAttributes.fillColor = fontColor;
    return txt;
}
```

**3. Define All Constants from Design Spec**
```jsx
var colors = {
    primary: rgb(66, 133, 244),
    surface: rgb(248, 249, 250),
    text_primary: rgb(32, 33, 36),
    divider: rgb(218, 220, 224)
};

var spacing = {xs: 8, sm: 12, md: 16, lg: 24, xl: 32};
var strokeWeights = {heavy: 3, medium: 2, light: 1};
var cornerRadius = 4;

var fonts = {
    primary: "Helvetica Neue",
    mono: "Menlo"
};

var typescale = {
    headline: {size: 28, weight: 600},
    body: {size: 14, weight: 400},
    caption: {size: 11, weight: 400}
};
```

**4. Create Elements in Visual Layer Order (Back to Front)**

```jsx
// Layer 1: Page background (if needed)
rectangle(600, 0, 800, 600, colors.surface, null, 0);

// Layer 2: Structural elements (cards, regions, dividers)
rectangle(500, 24, 352, 400, rgb(255, 255, 255), colors.divider, 1);
// ... more cards, borders, grid lines

// Layer 3: Connectors and arrows (if needed)
// ... draw lines, curves, arrows

// Layer 4: Text frames LAST (renders on top)
textFrame("Title", 24, 76, 352, 56, 28, fonts.primary, colors.text_primary);
textFrame("Body text", 24, 100, 352, 200, 14, fonts.primary, colors.text_secondary);
// ... more text
```

**Why back-to-front?**
Illustrator renders elements in the order they're added to the document. Text added last renders on top of shapes. Previous approaches (adding text first) caused text to render behind card backgrounds, making it invisible.

**5. Auto-Size Artboard to Content (Optional)**
```jsx
doc.artboards[0].artboardRect = [0, doc.height - 600, 800, 0];
```

**6. Export PNG at 200% Scale**
```jsx
var exportOptions = new ExportOptionsPNG24();
exportOptions.antiAliasing = true;
exportOptions.transparency = true;
exportOptions.horizontalScale = 200;
exportOptions.verticalScale = 200;

var exportFile = new File("/mnt/<username>/Desktop/diagram_preview.png");
doc.exportFile(exportFile, ExportType.PNG24, exportOptions);

alert("Exported: " + exportFile.fsName);
```

### Phase 3: Execute and Evaluate

After running the JSX and reading the exported PNG:

**Grid Alignment Checklist:**
- Margins correct? (spec: 24pt on all sides)
- Gutters visible and correctly sized? (spec: 16pt)
- Elements align to base_unit grid (8pt)?
- Content contained within artboard bounds?

**Type Hierarchy Checklist:**
- Title/display larger and bolder than body? (visual contrast)
- Body text readable at preview size?
- Captions distinct from body (size, weight, or color)?
- Font family and weight match spec (e.g., "Helvetica Neue Bold" vs. "Menlo Regular")?

**Color Checklist:**
- Colors match spec RGB values (not off by ~5 points)?
- Contrast sufficient? (text on background passes WCAG AA: 4.5:1 for normal text)?
- Divider lines visible but subtle? (expected: light gray)

**Composition Checklist:**
- Layout matches visual encoding plan (two-column, grid, etc.)?
- Visual hierarchy (size, color, position) guides eye correctly?
- No overlapping elements?
- Spacing between elements consistent with spec?

**Bounding Box Containment Checklist:**
Every element must fit inside its parent container. Check each container (column
background, card, group rect) and verify every child stays within bounds:
- Text labels beside shapes: shape width + gap + label width ≤ container width.
  Estimate text width as `charCount × fontSize × 0.55` for Helvetica.
- Child shapes inside cards: shape bottom edge (`y + height` for rects,
  `cy + radius` for circles) ≤ card bottom edge. Scale or reposition if not.
- Text overlapping adjacent shapes: ensure the text bounding box and shape
  bounding box don't intersect. Add a gap or shift one element.
- Arrow/arrowhead overshoot: arrowheads extend past the line end. Keep arrows
  clear of container edges by the arrowhead length.

This is the most common class of rendering bug. Run this check explicitly on
every container before moving to the next evaluation step.

**24-Rule Audit from Skill 3 (Sample)**
If applicable:
- Rule: "All headline text is bold" → Is all headline text visibly bold?
- Rule: "Background panels have 4pt corners" → Do corners appear rounded?
- Rule: "Dividers are 1pt stroke in color.divider" → Are they the right color and weight?

### Common Evaluation Failures and Fixes

| Failure | Cause | Fix |
|---------|-------|-----|
| Text too small or unreadable | Font size too low, or size applied to wrong text frame | Increase fontSize in spec or reduce content volume. Check font size constants. |
| Text overlapping or clipped | Text frame width too narrow or height too short | Increase text frame width/height. Use `txt.height = height + padding`. |
| Colors don't match spec (too bright/dark) | RGB values inverted or mistyped | Check spec values: `[66, 133, 244]` not `[244, 133, 66]`. Verify against actual PNG. |
| Elements misaligned | Position math error (forgot unit conversion or coordinate flip) | Recalculate: `illustrator_y = artboard_height - design_spec_y`. Verify grid math. |
| Shapes have wrong stroke | strokeWeight or strokeColor not set | Set both `.stroked = true` and `.strokeColor`, `.strokeWidth`. |
| MRAP error / "app is busy" | Stale Illustrator document or missing delay | Run document cleanup script. Add `delay 2` after closing. Retry. |
| JSX syntax error | Typo in JSX, missing semicolon | Check error message in Script Editor. Review JSX for typos, unclosed braces. |
| PNG not exported or blank | Export path wrong, or doc not saved | Verify export path exists on Mac. Check `File` object creation. Debug with `alert()`. |
| Label overflows container | Shape + gap + label wider than container | Reduce shape width; compute max from container bounds minus label space. |
| Child shape exceeds card | Shape bottom edge below card bottom | Scale shape height to fit; anchor to card bottom and grow upward. |
| Text overlaps adjacent shape | Text bbox intersects shape bbox | Shift shape or text; ensure gap between text right edge and shape left edge. |

### Phase 4: Iterate

The render-evaluate-fix loop typically takes **2–4 iterations** to reach specification.

**Per iteration:** Fix **2–3 issues maximum** to avoid cascading problems:

1. **Iteration 1:** Evaluate grid alignment + type readability. Fix position math and font sizes.
2. **Iteration 2:** Evaluate colors + composition. Adjust stroke weights and color assignments.
3. **Iteration 3:** Fine-tune spacing and text frame dimensions. Re-export.
4. **Iteration 4 (if needed):** Verify 24-rule audit. Final polish.

Each fix should change one or two values (e.g., font size and text frame height). Then re-run, re-evaluate. This prevents introducing new bugs.

**Example iteration:**
```
Iteration 1: "Body text too small"
Fix: Change typescale.body.size from 12 to 14
Re-run JSX, re-export PNG, check readability ✓

Iteration 2: "Title color doesn't match spec (too blue)"
Fix: Change colors.primary from rgb(66, 133, 244) to rgb(60, 130, 240)
Re-run, compare to spec... still not exact. Adjust to rgb(66, 133, 244) per YAML.
✓

Iteration 3: "Margins look too wide on left"
Fix: Check grid math. margins.left = 24, but text starting at x=40.
Change to x=24. Re-run. ✓

Specification now met.
```

### Phase 5: Final Export

Once the diagram passes evaluation:

**1. PNG for Digital Review**
```jsx
// Already exported at 200% during iteration.
// Copy to workspace folder:
cp /mnt/<username>/Desktop/diagram_preview.png /sessions/vibrant-peaceful-allen/diagram_final.png
```

**2. Optional: Save .ai File**
```jsx
doc.save(new File("/mnt/<username>/Desktop/diagram.ai"));
```

**3. Optional: Export PDF for Print**
```jsx
var pdfOptions = new PDFSaveOptions();
doc.saveAs(new File("/mnt/<username>/Desktop/diagram.pdf"), pdfOptions);
```

**4. Clean Up and Copy to User**
```bash
# Copy final files to session workspace
cp /mnt/<username>/Desktop/diagram.ai /sessions/vibrant-peaceful-allen/
cp /mnt/<username>/Desktop/diagram.pdf /sessions/vibrant-peaceful-allen/
```

Provide the user with the final PNG and link to the .ai file for future editing.

---

## Working with Existing AI Files

If iterating on an existing diagram:

**1. Open Existing File**
```jsx
var doc = app.open(new File("/path/to/existing.ai"));
```

**2. Read Structure (Optional)**
Document structure is visible in the Illustrator UI. Script can query:
```jsx
for (var i = 0; i < doc.pathItems.length; i++) {
    var item = doc.pathItems[i];
    // item.left, item.top, item.width, item.height, item.fillColor, etc.
}
```

**3. Modify Elements**
Update existing elements without recreating the entire diagram:
```jsx
doc.pathItems[0].left = 50;  // Move first shape
doc.textFrames[0].contents = "New text";  // Update text
```

**4. Re-export**
After modifications, export PNG and evaluate as usual.

Refer to `references/extendscript-api.md` Section 4 for full details on document structure, querying elements, and batch operations.

---

## References

**API & Patterns:**
- `references/extendscript-api.md` — Complete ExtendScript API for Illustrator. Sections 1-7 cover core objects, properties, methods. Section 8: coordinate system. Section 10: text formatting. Section 13: MRAP error handling and recovery.

**Composition Implementations:**
- `references/composition-implementations.md` — JSX code patterns for each composition type (two-column, single-flow, radial, matrix, network). Copy patterns directly into Phase 2 script build.

**Design System Reference:**
- Skill 3 output (diagram-graphic-design) — The YAML spec. Always available in context as the immediate upstream input.

---

## Tone and Scope

This skill is **implementation-focused**, not creative:
- It executes design decisions made in Skills 2 and 3 faithfully.
- It does not question the composition, color palette, or type hierarchy. Those are upstream.
- It does translate specifications into JSX precisely, handle platform quirks (AppleScript, coordinate systems, MRAP errors), and verify output against spec.
- Its success metric: the exported PNG visually matches the design specification within acceptable tolerance.

Use this skill when you have a complete design specification and need the actual Illustrator file. Use Skill 3 (diagram-graphic-design) if you still need to decide colors, type scale, or layout. Use Skill 2 (diagram-visual-encoding) if you're unsure how to visually represent your data.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/darcyegb) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
