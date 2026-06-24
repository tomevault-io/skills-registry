---
name: hostile-reviewer
description: Gemini-native multi-model adversarial code review — iterative convergence loop for PRs and plans Use when this capability is needed.
metadata:
  author: OmniNode-ai
---

# Hostile Reviewer Skill (Gemini Edition)

Multi-model adversarial review with iterative convergence, leveraging Gemini's 2M+ token window to perform "whole-project" reviews that identify structural flaws and architectural drift.

## Workflow
1. **Multi-Model Pass**: Gemini orchestrates a review pass across multiple models (Gemini, Codex, DeepSeek-R1).
2. **Finding Aggregation**: Gemini analyzes and deduplicates findings from all models, identifying "weighted-union" risks.
3. **Iterative Convergence**: Automatically applies fixes and re-runs the review until 2 consecutive passes produce no findings above NIT severity.
4. **Disagreement Triage**: Explicitly surfaces and triages disagreements between different models for human consideration.

## Gemini Advantages
- **Deep Architectural Grounding**: Gemini sees the entire PR or plan in the context of the *entire* codebase, identifying breaking changes that local diff-based reviews miss.
- **Superior Aggregation**: More intelligently merges findings from other models by understanding the technical intent behind their critiques.
- **Analytical-Strict Persona**: Maintains a rigorous, PhD-level domain expertise posture across the entire review loop.

## Arguments
- `--pr <N>`: PR number to review.
- `--file <path>`: Plan file to review.
- `--models`: Comma-separated model list (default: codex,deepseek-r1).
- `--passes`: Fixed number of passes (default: iterates to convergence).

---
> Source: [OmniNode-ai/omnigemini](https://github.com/OmniNode-ai/omnigemini) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
