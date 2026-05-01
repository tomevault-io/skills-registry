---
name: llm-evaluator
description: > Use when this capability is needed.
metadata:
  author: openclaw
---

# LLM Evaluator ⚖️

LLM-as-a-Judge evaluation system powered by Langfuse. Uses GPT-5-nano to score AI outputs.

## When to Use

- Evaluating quality of search results or AI responses
- Scoring traces for relevance, accuracy, hallucination detection
- Batch scoring recent unscored traces
- Quality assurance on agent outputs

## Usage

```bash
# Test with sample cases
python3 {baseDir}/scripts/evaluator.py test

# Score a specific Langfuse trace
python3 {baseDir}/scripts/evaluator.py score <trace_id>

# Score with specific evaluator only
python3 {baseDir}/scripts/evaluator.py score <trace_id> --evaluators relevance

# Backfill scores on recent unscored traces
python3 {baseDir}/scripts/evaluator.py backfill --limit 20
```

## Evaluators

| Evaluator | Measures | Scale |
|-----------|----------|-------|
| relevance | Response relevance to query | 0–1 |
| accuracy | Factual correctness | 0–1 |
| hallucination | Made-up information detection | 0–1 |
| helpfulness | Overall usefulness | 0–1 |

## Credits

Built by [M. Abidi](https://www.linkedin.com/in/mohammad-ali-abidi) | [agxntsix.ai](https://www.agxntsix.ai)
[YouTube](https://youtube.com/@aiwithabidi) | [GitHub](https://github.com/aiwithabidi)
Part of the **AgxntSix Skill Suite** for OpenClaw agents.

📅 **Need help setting up OpenClaw for your business?** [Book a free consultation](https://cal.com/agxntsix/abidi-openclaw)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
