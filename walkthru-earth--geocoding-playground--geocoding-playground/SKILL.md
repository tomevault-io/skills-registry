---
name: test-autocomplete
description: > Use when this capability is needed.
metadata:
  author: walkthru-earth
---

# Autocomplete Testing Skill

Validate the smart autocomplete engine against documented test scenarios.

## Workflow

1. **Read the test scenarios** from `_study/AUTOCOMPLETE.md` (Test Scenarios section).

2. **Read the relevant source files**:
   - `packages/core/src/autocomplete.ts` - classifyInput(), suggest(), rankSuggestions()
   - `packages/core/src/address-parser.ts` - getParser(), POSTCODE_RE, NUMBER_FIRST
   - `packages/core/src/search.ts` - suggestionScore(), SearchCache

3. **Trace the flow** for the user's test case:
   - What does `classifyInput(input, cc)` return? (street/postcode/mixed/ready)
   - What parser does `getParser(cc)` return?
   - What does `parseAddress(input)` extract? (street, number, postcode, unit)
   - What SQL would `buildStreetSQL()` or `buildPostcodeSQL()` generate?
   - How would `rankSuggestions()` order the results with the given city?
   - City search now uses `searchCities()` JS array search (not DuckDB SQL). It does prefix match with contains fallback + similarity ranking
   - `preNormalize()` can be used to pre-compute normalized names at prefetch time for faster search

4. **Check for regressions** against the 3 known issues:
   - Issue 1: Duplicate cities (GROUP BY region, city dedup)
   - Issue 2: City boost in ORDER BY (list_has_any tile overlap)
   - Issue 3: Word match > prefix match (suggestionScore tiers)

5. **Report** whether the code correctly handles the test case, with the exact
   classification, SQL, and ranking that would be produced.

## Key Constants to Check
- `NUMBER_FIRST` set in address-parser.ts must match pipeline SQL (addresses.sql line 143)
- `POSTCODE_RE` patterns per country
- `suggestionScore` tiers: 100=exact, 80=word, 60=prefix, 40=substring, 0-30=Jaccard

---
> Source: [walkthru-earth/geocoding-playground](https://github.com/walkthru-earth/geocoding-playground) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-18 -->
