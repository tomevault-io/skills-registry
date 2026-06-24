---
name: content-localizer
description: Content localization between Japanese and English — not just translation but cultural adaptation, market-specific references, and platform conventions for each language market. Expand reach to international audiences or localize overseas content for Japanese readers. Use when this capability is needed.
metadata:
  author: Gaura3560
---

# Content Localizer

Expand your content to new language markets — culturally adapted, not just translated.

## When to Activate

- User says `/translate {file} en` or `/translate {file} ja`
- User says `/localize {file} {target_lang}`
- User asks "translate this to English"
- User asks "localize this overseas article for Japanese readers"

## Prerequisites

- `~/.content-autopilot/profile.json` must exist
- Source content file to localize

## Commands

### `/translate {file_path} en` — Localize Japanese content for English market
### `/translate {file_path} ja` — Localize English content for Japanese market
### `/translate` — Interactive: select source and target

## Localization vs Translation

| Aspect | Translation | Localization (what we do) |
|--------|------------|--------------------------|
| Text | Word-for-word conversion | Meaning-preserving adaptation |
| Examples | Keep original references | Replace with locally relevant ones |
| Data | Same statistics | Add local market data |
| Humor | Direct translation (often fails) | Culturally appropriate equivalent |
| CTA | Same call to action | Platform-appropriate CTA |
| Length | ~Same | Adjusted for platform norms |
| SEO | Same keywords translated | Locally researched keywords |

## Workflow

### Step 1: Load Source Content

Read the file and identify:
- Source language (auto-detect from content)
- Content type (article, thread, caption)
- Key concepts, data, examples, references
- Cultural references that need adaptation

### Step 2: Cultural Analysis

Identify elements that need localization:

```
Cultural Adaptation Required:

1. Reference: "note.com" → English equivalent
   Adaptation: "Medium" or "Substack" (similar platform)

2. Data: "Japanese market: 80% of..."
   Adaptation: Add global/US market equivalent data

3. Example: "{Japanese company case study}"
   Adaptation: Replace with internationally known equivalent

4. Idiom: "{Japanese expression}"
   Adaptation: English equivalent that carries the same meaning

5. Platform: note → Medium, X stays X, Instagram stays Instagram
```

### Step 3: Localize Content

**Japanese → English:**
```
Adaptations:
- Replace Japanese market data with global/US equivalents
- Swap Japanese case studies for international ones
- Adjust currency (¥ → $) with approximate equivalents
- Use English platform conventions (Medium instead of note)
- Add context for Japan-specific concepts
- Research English SEO keywords for the topic
- Adjust tone (Japanese professional → English professional)
```

**English → Japanese:**
```
Adaptations:
- Add Japanese market context and data
- Replace Western case studies with Japanese or Asian equivalents
- Adjust currency ($ → ¥)
- Use Japanese platform conventions (note instead of Medium)
- Explain Western concepts that may be unfamiliar
- Research Japanese SEO keywords
- Adjust tone to match Japanese content norms
- Localize humor and cultural references
```

### Step 4: Platform Adaptation

Adjust for platform conventions in the target market:

| Platform | Japanese Market | English Market |
|----------|----------------|----------------|
| Long-form | note | Medium, Substack |
| Short-form | X (same) | X (same) |
| Visual | Instagram (same) | Instagram (same) |
| Hashtags | Japanese + some English | English + trending tags |
| CTA style | Polite, indirect | Direct, action-oriented |
| Emoji | Common in business | More casual, less in B2B |

### Step 5: SEO Localization

Research target-language keywords:
```
Search: "{topic}" in {target_language}
Search: "{keyword} {target_language} trends"
```

Replace keywords in:
- Title
- Headings
- First paragraph
- CTA

### Step 6: Save Output

```
~/Desktop/content-autopilot-output/
  localized_{lang}_{original_filename}
```

Example: `localized_en_note_2026-03-20.md`

### Step 7: Display Result

```
============================================
  Content Localized
============================================

Source: {source_lang} — "{original_title}"
Target: {target_lang} — "{localized_title}"

Adaptations made:
  - {count} cultural references adapted
  - {count} data points localized
  - {count} examples replaced
  - SEO keywords: {target_lang_keywords}

Output: ~/Desktop/content-autopilot-output/localized_{lang}_{filename}

--- Quality Check ---
[ ] Cultural references are appropriate for {target_market}
[ ] Data points are accurate for {target_market}
[ ] Tone matches {target_market} conventions
[ ] SEO keywords researched for {target_lang}

============================================
```

## Integration with Other Skills

- **trend-scout**: Source English articles → localize for Japanese market
- **repurpose**: Can chain with repurpose (translate + platform-adapt)
- **competitor-scout**: Compare with {target_market} competitors
- **content-analytics**: Track localized content separately in history

## Quality Gate

- [ ] Localization, not just translation (cultural adaptation applied)
- [ ] All data points verified for target market
- [ ] Examples are relevant to target audience
- [ ] Platform conventions match target market
- [ ] SEO keywords researched in target language
- [ ] Tone is natural for target language readers
- [ ] No untranslated sections or mixed-language issues

---
> Source: [Gaura3560/content-autopilot](https://github.com/Gaura3560/content-autopilot) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
