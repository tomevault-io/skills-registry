---
name: project-development
description: This skill should be used when the user asks to "start an LLM project", "design batch pipeline", "evaluate task-model fit", "structure agent project", or mentions pipeline architecture or choosing between LLM and traditional approaches. Use when this capability is needed.
metadata:
  author: bthillerup
---

# Project Development Methodology

This skill covers principles for identifying tasks suited to LLM processing, designing effective project architectures, and iterating rapidly using agent-assisted development.

## When to Activate

Activate this skill when:
- Starting a new project that might benefit from LLM processing
- Evaluating whether a task is suited for agents vs traditional code
- Designing architecture for an LLM-powered application
- Choosing between single-agent and multi-agent approaches

## Task-Model Fit Recognition

### LLM-Suited Tasks

| Characteristic | Why It Fits |
|----------------|-------------|
| Synthesis across sources | LLMs excel at combining information |
| Subjective judgment with rubrics | LLMs handle grading, evaluation, classification |
| Natural language output | When goal is human-readable text |
| Error tolerance | Individual failures don't break system |
| Batch processing | No conversational state required |
| Domain knowledge in training | Model already has context |

### LLM-Unsuited Tasks

| Characteristic | Why It Fails |
|----------------|--------------|
| Precise computation | Math, counting unreliable |
| Real-time requirements | LLM latency too high |
| Perfect accuracy requirements | Hallucination risk |
| Proprietary data dependence | Model lacks context |
| Deterministic output requirements | Same input must produce identical output |

## The Manual Prototype Step

Before investing in automation, validate task-model fit with a manual test:
1. Copy one representative input into model interface
2. Evaluate output quality
3. This takes minutes and prevents hours of wasted development

## Pipeline Architecture

LLM projects benefit from staged pipelines where each stage is discrete, idempotent, cacheable, and independent:

```
acquire → prepare → process → parse → render
```

1. **Acquire**: Fetch raw data from sources
2. **Prepare**: Transform data into prompt format
3. **Process**: Execute LLM calls (expensive, non-deterministic)
4. **Parse**: Extract structured data from LLM outputs
5. **Render**: Generate final outputs

## File System as State Machine

Use filesystem to track pipeline state:

```
data/{id}/
├── raw.json         # acquire complete
├── prompt.md        # prepare complete
├── response.md      # process complete
├── parsed.json      # parse complete
```

To check if item needs processing: check if output file exists.
To re-run a stage: delete its output file and downstream files.

## Structured Output Design

Prompt design determines parsing reliability:

```
Analyze the following and provide response in exactly this format:

## Summary
[Your summary here]

## Score
Rating: [1-10]

Follow this format exactly because I will be parsing it programmatically.
```

Build parsers that handle variations gracefully—LLMs don't follow instructions perfectly.

## Architectural Reduction

Start with minimal architecture. Add complexity only when proven necessary.

**When reduction outperforms complexity**:
- Your data layer is well-documented
- The model has sufficient reasoning capability
- Specialized tools were constraining rather than enabling
- You're spending more time maintaining scaffolding than improving outcomes

## Project Planning Template

1. **Task Analysis**: Input/output, error tolerance, value per completion
2. **Manual Validation**: Test one example with target model
3. **Architecture Selection**: Single pipeline vs multi-agent
4. **Cost Estimation**: Items × tokens × price + 20-30% buffer
5. **Development Plan**: Stage-by-stage with testing strategy

## Anti-Patterns

- **Skipping manual validation**: Wastes time when approach is flawed
- **Monolithic pipelines**: Makes debugging difficult
- **Over-constraining**: Adding guardrails the model could handle
- **Ignoring costs until production**: Token costs compound
- **Perfect parsing requirements**: Build robust parsers for variations

## Guidelines

1. Validate task-model fit with manual prototyping before building automation
2. Structure pipelines as discrete, idempotent, cacheable stages
3. Use file system for state management and debugging
4. Design prompts for structured, parseable outputs
5. Start with minimal architecture; add complexity when proven necessary
6. Estimate costs early and track throughout development

---

**Created**: 2025-12-25 | **Version**: 1.0.0

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/bthillerup) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
