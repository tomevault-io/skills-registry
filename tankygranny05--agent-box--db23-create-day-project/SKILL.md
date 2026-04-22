---
name: db23-create-day-project
description: Create a new Viber Stock OCR project folder for a new trading day. This skill should be used when the user wants to set up a fresh project folder for processing stock transaction screenshots from a new date. Triggers on requests like "create new day project", "set up folder for 2025-12-25", or "initialize new OCR project from old day". Use when this capability is needed.
metadata:
  author: tankygranny05
---

# DB23 Create Day Project

## Overview

This skill guides the creation of a new Viber Stock Image OCR project folder for a new trading day, based on an existing day's project structure. It ensures the new project has the correct folder structure, copies only reusable templates (not old data), and updates CLAUDE.md to be specific to the new day.

## When to Use

- User wants to process stock screenshots for a new date
- User provides a path to an old project folder (e.g., `/Users/sotola/Desktop/2025_12_23`)
- User wants to create a new folder like `/Users/sotola/Desktop/2025_12_25`

## Workflow

### Step 1: Gather Information

Ask the user for:
1. **Old project path** - e.g., `/Users/sotola/Desktop/2025_12_23`
2. **New project date** - e.g., `2025-12-25` (will create folder `2025_12_25`)
3. **New project location** - usually same parent directory as old project

### Step 2: Create Directory Structure

Create the new project folder with these subdirectories:

```
{new_date}/
├── segmentation_outputs/     # For official segmented images (empty)
├── ocr_outputs/              # For OCR Python files and CSVs (empty)
├── soto_docs/                # For documentation (empty)
└── ai/
    ├── generated_data/       # For WIP crops with suffix (empty)
    ├── generated_doc/        # For task prompts (copy from old)
    └── generated_docs/       # For additional docs (empty)
```

### Step 3: Copy Reusable Files

**COPY these files from old project:**

| File | Purpose |
|------|---------|
| `CLAUDE.md` | Main context doc (will be edited in Step 5) |
| `stock_mappings.txt` | StockNum → Ticker mapping (usually unchanged) |
| `ocr_prompt_for_gemini.md` | OCR instructions template |
| `ai/generated_doc/task_*.md` | Task prompts for agents |

**DO NOT copy:**

| Pattern | Reason |
|---------|--------|
| `viber_image_*.jpg` | Old day's screenshots |
| `segmentation_outputs/*` | Old day's cropped images |
| `ocr_outputs/*` | Old day's OCR results |
| `ai/generated_doc/split_detection_*.md` | Old day's analysis results |
| `ai/generated_doc/stock_observation_*.md` | Old day's observations |
| `ai/generated_docs/*.md` | Old day's reports |
| `soto_docs/*.md` | Old day's documentation |
| `soto_docs/*.json` | Old day's state files |

### Step 4: Create Empty known_issues.txt

Create an empty `known_issues.txt` file in the new project root.

### Step 5: Update CLAUDE.md for New Day

Edit the copied CLAUDE.md to update these sections:

**Section 1.2 - Known Split Stocks:**
- Clear or comment out the old split stock information
- Add placeholder text: "Split stocks not yet determined for this day. Run split detection first."

**Section 5.2 - Image Contents Summary:**
- Clear the old image-to-stock mapping table
- Add placeholder: "Image contents not yet analyzed. Add viber images and run split detection."

**DO NOT change:**
- Stock number range (still 4-12)
- Stock mappings reference
- File naming conventions
- Pipeline structure
- Task prompt references

### Step 6: Remind User of Next Steps

After creating the project, remind the user:

1. **Add Viber images** - Copy/move `viber_image_*.jpg` files to new project
2. **Rename images sequentially** - `viber_image_1.jpg`, `viber_image_2.jpg`, etc.
3. **Run split detection** - Analyze which stocks span multiple images
4. **Update CLAUDE.md** - Fill in Section 1.2 (split stocks) and Section 5.2 (image contents)

## Example Usage

```
User: Create a new project for December 25th based on /Users/sotola/Desktop/2025_12_23

Agent:
1. Creates /Users/sotola/Desktop/2025_12_25/
2. Creates subdirectories (segmentation_outputs, ocr_outputs, ai/generated_data, etc.)
3. Copies CLAUDE.md, stock_mappings.txt, ocr_prompt_for_gemini.md, task_*.md
4. Creates empty known_issues.txt
5. Updates CLAUDE.md Section 1.2 and 5.2 with placeholders
6. Reports completion and next steps
```

## File Checklist

After completion, the new project should have:

```
{new_date}/
├── CLAUDE.md                 ✓ (copied + edited)
├── stock_mappings.txt        ✓ (copied)
├── ocr_prompt_for_gemini.md  ✓ (copied)
├── known_issues.txt          ✓ (created empty)
├── segmentation_outputs/     ✓ (empty dir)
├── ocr_outputs/              ✓ (empty dir)
├── soto_docs/                ✓ (empty dir)
└── ai/
    ├── generated_data/       ✓ (empty dir)
    ├── generated_doc/
    │   ├── task_detect_stock_split.md      ✓
    │   ├── task_segment_stock.md           ✓
    │   ├── task_segment_stock_text.md      ✓
    │   └── task_segment_verification.md    ✓
    └── generated_docs/       ✓ (empty dir)
```

No `viber_image_*.jpg` files yet - user adds these separately.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tankygranny05) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
