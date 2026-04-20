---
name: lattereview-systematic-review-automation
description: | Use when this capability is needed.
metadata:
  author: tajo9128
---

# LatteReview Skill

This skill integrates the [LatteReview](https://github.com/PouriaRouzrokh/LatteReview) framework to allow Agent Zero to perform PhD-level systematic reviews.

## Capabilities

1.  **Screening**: Filter papers based on inclusion/exclusion criteria.
2.  **Scoring**: Verify relevance with confidence scores.
3.  **Abstraction**: Extract structured data points from papers.

## Usage in Agent Zero

```python
from agent_zero.skills.latte_review import get_latte_review
latte = get_latte_review()

# Define Criteria
inclusion = "Studies using AI for drug discovery in oncology."
exclusion = "Review articles, non-English papers."

# Run Screening
output_csv = latte.screen_papers(
    input_path="data/papers/candidates.csv", # Must have 'title' and 'abstract' columns
    inclusion_criteria=inclusion,
    exclusion_criteria=exclusion
)
print(f"Screening complete. Results saved to {output_csv}")
```

## Configuration

The skill uses `LiteLLM` under the hood. It will inherit the API keys from the Agent Zero environment (e.g., `OPENAI_API_KEY`, `GEMINI_API_KEY`).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tajo9128) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
