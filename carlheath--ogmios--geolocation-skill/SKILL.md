---
name: geolocation-skill
description: | Use when this capability is needed.
metadata:
  author: carlheath
---

# Geolocation Reasoning

**Method:** Iterative reasoning loop (max 5 iterations)
**Inspiration:** GeoVista research framework

## Core Workflow

### 1. Visual Clue Extraction

| Category | Look for |
|----------|----------|
| **Text/Signage** | Languages, scripts, street signs, license plates, phone formats |
| **Infrastructure** | Road markings, traffic lights, poles, utilities, bollards |
| **Architecture** | Materials, styles, roof types, density patterns |
| **Nature** | Vegetation, terrain, geology, sun position, weather |
| **Culture** | Vehicles, driving side, clothing, shops, brands |

### 2. Hypothesis Formation

Form hypotheses at multiple scales:

| Scale | Example |
|-------|---------|
| Continent/Region | Europe, Southeast Asia |
| Country | Specific nation |
| Region/State | Sub-national |
| City/Town | Locality |
| Precise | Street-level |

For each: Note supporting evidence, confidence (H/M/L), conflicts.

### 3. Image Examination

When details unclear:
- Crop regions of interest
- Enhance text/signage
- Isolate architectural details

### 4. Web Search Verification

| Strategy | Query pattern |
|----------|---------------|
| Sign text | `"[exact text]" location` |
| Features | `[feature] [suspected region]` |
| Business | `"[name]" address` |
| Landmark | `[description] landmark [region]` |
| Road | `[designation] [country]` |

Cross-reference multiple clues, look for consistent attribution.

### 5. Iterative Refinement

After each cycle:
1. Update hypothesis confidence
2. Identify remaining uncertainties
3. Determine if more examination helps
4. Continue until confident or limit reached

## Output Format

```
**Location Analysis**

Observed Clues:
- [Key observations with confidence]

Reasoning Process:
- [Hypothesis formation and verification]

**Conclusion**
- Most likely location: [As specific as evidence allows]
- Confidence: High/Medium/Low
- Key evidence: [Primary determinants]
- Uncertainties: [What remains unclear]
```

## Important Considerations

| Aspect | Guideline |
|--------|-----------|
| Confidence | High requires multiple independent confirmations |
| Single clues | Can mislead (similar signage in multiple countries) |
| Privacy | Don't identify individuals or residential addresses |
| Iteration | Max 5 cycles, stop when marginal value low |

## References

- `references/visual-clues.md` — Clue categories
- `references/regional-signatures.md` — Region-specific indicators
- `references/verification-strategies.md` — Search patterns

---

🎯 COMPLETED: [SKILL:geolocation-skill] [location identified from image]
🗣️ CUSTOM COMPLETED: [SKILL:geolocation-skill] [Location found]

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/carlheath) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
