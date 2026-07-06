---
name: travel-recommendations
description: Preferred travel season or 'current Use when this capability is needed.
metadata:
  author: IBM
---

# Travel Recommendations Skill

This skill provides personalized travel destination recommendations based on user preferences, budget, and season.

## Features

- Destination suggestions tailored to interests
- Budget-appropriate recommendations
- Seasonal considerations
- Activity suggestions for each destination
- Estimated costs and travel tips
- Cultural highlights and must-see attractions

## Usage

The skill accepts travel preferences, budget level, and preferred season to generate recommendations.

## Example

```python
result = travel_recommendations(
    preferences="beach and culture",
    budget="moderate",
    season="summer"
)
```

## Output Format

Returns a formatted guide with:
- Recommended destinations (3-5 locations)
- Why each destination fits the criteria
- Estimated budget ranges
- Best time to visit
- Top activities and attractions
- Travel tips

## Notes

- Considers current travel trends and seasonal factors
- Provides diverse options across different regions
- Includes practical travel advice
- Budget estimates in USD (can be converted using currency_converter skill)

---
> Source: [IBM/CodeEngine](https://github.com/IBM/CodeEngine) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-07-06 -->
