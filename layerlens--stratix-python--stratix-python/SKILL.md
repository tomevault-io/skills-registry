---
name: layerlens
description: > Use when this capability is needed.
metadata:
  author: LayerLens
---

# LayerLens Skill for OpenClaw

This skill lets OpenClaw interact with the [LayerLens](https://layerlens.ai/)
AI evaluation platform. Use it to upload traces of agent executions, create
quality judges, run evaluations, and retrieve scored results.

## Prerequisites

Install the LayerLens Python SDK:

```bash
pip install layerlens --index-url https://sdk.layerlens.ai/package
```

Set your API key:

```bash
export LAYERLENS_STRATIX_API_KEY=your-api-key
```

## What This Skill Does

When triggered, this skill:

1. **Uploads a trace** -- captures the input (task) and output (agent response)
   as a LayerLens trace with metadata about the execution context.
2. **Creates a judge** -- defines an evaluation rubric based on the requested
   quality dimension (safety, accuracy, helpfulness, etc.).
3. **Runs an evaluation** -- scores the trace against the judge criteria.
4. **Returns results** -- provides a pass/fail verdict, numeric score, and
   reasoning explanation.

## Usage

Ask OpenClaw to evaluate an output:

```
Evaluate the last response for safety using LayerLens.
```

```
Run a quality check on this output: "The capital of France is Berlin."
```

```
Upload a trace of our conversation and score it for helpfulness.
```

## Evaluation Script

The skill delegates to `scripts/evaluate.py`, which accepts input via stdin
or command-line arguments:

```bash
# Via arguments
python scripts/evaluate.py --input "What is 2+2?" --output "2+2 is 4." --goal "factual accuracy"

# Via stdin (JSON)
echo '{"input": "What is 2+2?", "output": "2+2 is 4.", "goal": "factual accuracy"}' | python scripts/evaluate.py
```

## SDK Reference

The skill uses these LayerLens SDK methods:

- `client.traces.upload(path)` -- upload a JSONL trace file
- `client.judges.create(name=, evaluation_goal=)` -- create an evaluation judge
- `client.trace_evaluations.create(trace_id=, judge_id=)` -- run an evaluation
- `client.trace_evaluations.get_results(evaluation_id)` -- retrieve results

See the [LayerLens Python SDK documentation](https://layerlens.ai/docs/sdk/python)
for full API details.

---
> Source: [LayerLens/stratix-python](https://github.com/LayerLens/stratix-python) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-19 -->
