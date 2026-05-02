---
name: midjourney-handouts
description: Generate campaign handouts using Midjourney with Trip 19 moodboard. Know when to use AI generation vs HTML for text-heavy documents. Use when this capability is needed.
metadata:
  author: kullendorff
---

# Midjourney Handouts for Trip 19

## Problem
Creating authentic 1940s-era handouts for Delta Green campaign that need:
- Visual authenticity (photos, aged documents, postcards)
- Exact text and dates (German postcards, business cards, newspaper articles)
- Consistent dark horror/thriller aesthetic

## Failed Approaches

### ❌ Attempt 1: Generate everything with Midjourney
- **Problem**: AI-generated text is unreliable
  - Dates become wrong ("15.6.39" → "15.8.42")
  - German handwriting looks "AI-gibberish"
  - Addresses get mangled
  - Cannot control exact layout
- **Result**: Wasted time regenerating, still got wrong text

### ❌ Attempt 2: Use v6.1 parameter
- **Problem**: Version parameter is obsolete
  - v7.x is current default
  - Adding `--v 6.1` is unnecessary
- **Result**: No benefit, just extra tokens in prompt

### ❌ Attempt 3: No moodboard selection
- **Problem**: Generated images didn't match campaign aesthetic
  - Too bright, generic AI look
  - Inconsistent style across handouts
- **Result**: Images looked out of place

## Working Solution

### Decision Matrix: Midjourney vs HTML

**Use Midjourney for:**
- ✅ Photographic images (landscapes, portraits, objects)
- ✅ Scenes requiring visual atmosphere
- ✅ Front sides of documents with visual content (postcard fronts)
- ✅ Period-appropriate imagery (1940s scenes, vintage photos)

**Use HTML for:**
- ✅ Documents with text (letters, newspapers, business cards, postcard backs)
- ✅ Material where exact text/dates/layout is critical
- ✅ Anything where AI-generation would produce incorrect text
- ✅ Complex layouts with specific structure

### Moodboard Workflow

**Before generating**, ensure Trip 19 moodboard is selected:

1. Navigate to Moodboards (left sidebar in Midjourney)
2. If multiple moodboards selected: Click "Unselect All" (top right)
3. Hover over "Trip 19" card
4. Click "Select" button that appears
5. Verify "Selected (1)" is shown and Trip 19 has "Selected" badge

**Why this matters:**
- Trip 19 moodboard provides consistent dark horror/thriller aesthetic
- Prevents generic AI look
- Maintains visual consistency across all campaign handouts

### Prompt Best Practices

**DO include:**
- Clear subject description
- Period details ("1939 German postcard", "vintage 1940s")
- Material characteristics ("aged paper", "sepia-toned", "faded colors")
- Aspect ratio: `--ar 3:2` or `--ar 16:9`
- Optional: `--style raw` for less AI-processed look

**DON'T include:**
- Version parameters (`--v 6.1`) - v7.x is default
- Exact text that needs to be correct (use HTML instead)
- Complex multi-line text layouts

**Example prompt:**
```
Vintage 1939 German postcard, front side showing picturesque Bavarian landscape
with alpine mountains and traditional village with church spire, sepia-toned
photographic print, faded warm colors, realistic aging with slight yellowing
and corner wear, white border typical of 1930s postcards, text at bottom
"Grüße aus Deutschland" in period-appropriate typography, authentic pre-war
German postcard aesthetic --ar 3:2 --style raw
```

### Download & File Management

**After generation:**

1. Click on generated image
2. Click menu icon (three dots) → "Download image"
3. Chrome downloads to `~/Downloads/` by default
4. Locate file:
   ```bash
   ls -lt ~/Downloads/ | head -5
   ```
5. Move to SL folder and rename:
   ```bash
   mv ~/Downloads/kullendorff_Long_filename_hash.png \
      "D:\GDRIVE\My Drive\Johan\Gaming\Gammal leka bäst\Delta Green\Trip19\SL\descriptive_name.png"
   ```
6. Verify:
   ```bash
   ls -lh "D:\GDRIVE\My Drive\Johan\Gaming\Gammal leka bäst\Delta Green\Trip19\SL\descriptive_name.png"
   ```

## Concrete Examples

### Example 1: German Postcard (Hybrid Approach)

**Front side** - Use Midjourney:
- Generated: `germany_postcard_1939_front.png`
- Visual content (Bavarian landscape) needs photographic quality
- Generic German text can be AI-generated

**Back side** - Use HTML:
- Created: `germany_postcard_1939_back.html`
- Exact German text: "Das Paket kommt wie besprochen. Bitte vorsichtig behandeln. - R"
- Specific postmark date: "FRANKFURT 15.6.39"
- Precise address layout to Senator Lundeen
- HTML ensures text accuracy, user takes screenshot

### Example 2: Business Card

**Front side** - HTML preferred:
- Created: `reinhardt_business_card.html`
- Exact name, title, address, phone number critical
- HTML allows precise typography and layout

**Back side** - HTML:
- Handwritten note simulation with CSS
- Exact text: "Kalenko - 1942"
- Rotated italic font mimics handwriting

### Example 3: Meta-Documentation

**Use Midjourney for:**
- Created: `late_night_coding_4am.png`
- Atmospheric scene of developer at 4 AM
- Visual storytelling, no exact text needed
- Dark moody aesthetic matches Trip 19 moodboard

## Best Practices

1. **Plan before generating**: Decide Midjourney vs HTML first
2. **Check moodboard**: Always verify Trip 19 is selected
3. **Keep prompts focused**: Describe visuals, not exact text
4. **Use descriptive filenames**: `germany_postcard_1939_front.png` not `image1.png`
5. **Store in SL folder**: All handouts go to `SL/` directory
6. **Document in HTML**: Link to generated images from SL guide pages

## Lessons Learned

**"Hammer and Nail" Problem:**
- Having Midjourney doesn't mean everything should be AI-generated
- HTML is often better for text-heavy documents
- Choose the right tool for the task

**Trial and Error Avoided:**
- Selecting moodboard first = consistent aesthetic
- Skipping version parameters = cleaner prompts
- Understanding Downloads workflow = faster file management

**Time Savings:**
- HTML for exact text = no regeneration loops
- Moodboard selection = first generation usually works
- Clear decision matrix = less second-guessing

## Related Documentation

- See `CLAUDE.md` → "Midjourney Moodboards" section
- See `CLAUDE.md` → "Handouts & Bildhantering" section
- See `.claude/agents/historical-handout-designer.md` for HTML handout creation

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kullendorff) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
