---
name: pptx-export-for-ppt-as-code
description: > Use when this capability is needed.
metadata:
  author: Russell-cell
---

# PPTX Export For PPT as Code

> Turn a `ppt-as-code` deck into `.pptx` by preserving the HTML result as slide screenshots instead of rebuilding editable PowerPoint layouts.

**Core Pipeline**: `Validate Handoff -> Load Contract -> Enter Export Mode -> Capture Slides -> Place Full-Slide Images -> Save output.pptx`

---

## Mandatory Rules

> [!IMPORTANT]
> ### Screenshot-Only Export
>
> - This skill supports **raster PPTX export only**.
> - Do not attempt native PowerPoint reconstruction.
> - Do not attempt partial-raster or mixed editable rendering.
> - The PPTX is a delivery container for the HTML deck's visual state.

> [!IMPORTANT]
> ### HTML Is The Design Source
>
> - The browser-rendered HTML deck is the design source of truth.
> - PPTX export preserves that rendered state.
> - Do not reinterpret layout through PowerPoint templates.

> [!IMPORTANT]
> ### Stable Export State Required
>
> - Every slide must be captured in a stable export state.
> - Hide presentation furniture that should not appear in the final deck:
>   - controls
>   - progress
>   - slide number
>   - debug overlays
> - Freeze motion and fragments to one chosen static state before capture.

> [!IMPORTANT]
> ### Product Boundary
>
> - This skill exports PPTX from `ppt-as-code` outputs.
> - It does not promise support for arbitrary hand-written HTML pages.
> - It is a final-delivery skill, not a deck-authoring skill.

> [!IMPORTANT]
> ### Fidelity Over Editability
>
> - The goal is visual fidelity, not editability.
> - If the user needs editable slides, that is a separate workflow and should not be implied by this skill.

> [!IMPORTANT]
> ### Asset Failure Policy
>
> - Do not silently drop missing images.
> - If a slide cannot be captured correctly because an asset is missing, stop with a clear blocker.

---

## Resource Manifest

### UI Metadata

| File | Path | Purpose |
|------|------|---------|
| skill interface metadata | `${SKILL_DIR}/agents/openai.yaml` | display name, short description, and default prompt |

### References

| Resource | Path | Runtime Use |
|----------|------|-------------|
| manifest contract | `${SKILL_DIR}/references/manifest-contract.md` | required for validating the handoff bundle |
| rendering rules | `${SKILL_DIR}/references/rendering-rules.md` | required for screenshot capture rules |

---

## Workflow

### Step 1: Validate Handoff

`GATE`: The request is to export a `ppt-as-code` deck to PowerPoint.

`EXECUTION`:

1. Confirm the handoff bundle includes:
   - `index.html`
   - `deck_manifest.json`
   - `assets/` when local images are expected
2. Confirm the manifest provides:
   - `deckTitle`
   - `slideSize`
   - `themeTokens`
   - `slides`
3. Reject the run early if the request is really about authoring or revising the deck instead of exporting it.

### Step 2: Load Export Contract

`GATE`: Step 1 complete.

`EXECUTION`:

1. Read `${SKILL_DIR}/references/manifest-contract.md`.
2. Read `${SKILL_DIR}/references/rendering-rules.md`.
3. Default the slide size to `16:9` when the manifest omits a value.

### Step 3: Enter Export Mode

`GATE`: Export inputs are valid enough to proceed.

`EXECUTION`:

1. Open the deck in export-safe mode.
2. Hide browser and reveal.js furniture that should not appear in PPTX.
3. Select one stable state for each slide.
4. Confirm that no browser chrome, controls, progress bars, or debug layers remain in frame.

### Step 4: Capture Slides

`GATE`: Export mode is stable.

`EXECUTION`:

1. Capture one screenshot per slide at presentation size.
2. Use the configured slide size from the manifest.
3. Preserve the full visible slide without browser chrome.
4. Store the resulting images in an export folder.

### Step 5: Build PPTX

`GATE`: All slide screenshots are available.

`EXECUTION`:

1. Create a PPTX deck with the target slide size.
2. Place each screenshot as a full-slide image.
3. Keep slide order identical to the HTML deck.

### Step 6: Save And Report

`GATE`: All slide images have been placed successfully.

`EXECUTION`:

1. Save the result as `output.pptx`.
2. Report:
   - slide count
   - screenshot count
   - missing-asset blockers if any

---

## Output Contract

Final output should include:

- `output.pptx`
- the folder of exported slide images
- a short summary of capture success or blockers

---
> Source: [Russell-cell/PPT-as-code](https://github.com/Russell-cell/PPT-as-code) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-24 -->
