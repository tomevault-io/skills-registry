---
name: agentic-paper-digest
description: Fetches and summarizes recent arXiv and Hugging Face papers. Use when you want a paper digest on quantitative finance, ML, or trading-related research. Use when this capability is needed.
metadata:
  author: tommyyeung660
---

# Agentic Paper Digest

## When to use
- Fetch recent paper digests from arXiv and Hugging Face during daily learning.
- Produce JSON output summarizing relevant papers for knowledge building.

## IMPORTANT: Usage Restrictions

**This skill is for the agent's own learning and research ONLY.**
- Use paper digests to expand quantitative finance and ML knowledge.
- You may propose project improvements based on research insights.
- **You MUST NOT directly modify any project code or data pipelines based on papers found.**
- **You MUST NOT integrate any paper's methodology into projects without human approval.**
- All project changes must be presented as "suggestions" for human review.

## Prerequisites
- Python 3 and network access.
- LLM access via `OPENAI_API_KEY` or OpenAI-compatible provider via `LITELLM_API_BASE` + `LITELLM_API_KEY`.
- `git` (for bootstrap).

## Setup (uv-based)

Bootstrap the repo:

```bash
bash "{baseDir}/scripts/bootstrap.sh"
```

Override clone location:

```bash
PROJECT_DIR="$HOME/agentic_paper_digest" bash "{baseDir}/scripts/bootstrap.sh"
```

## Run (CLI)

```bash
bash "{baseDir}/scripts/run_cli.sh"
```

Pass flags:

```bash
bash "{baseDir}/scripts/run_cli.sh" --window-hours 24 --sources arxiv,hf
```

## Configuration

Config files in `PROJECT_DIR/config`:
- `config/topics.json`: topics with id, label, description, max_per_topic, keywords
- `config/settings.json`: fetch limits
- `.env`: API keys and preferences

Key environment variables:
- `OPENAI_API_KEY` or `LITELLM_API_KEY`: LLM access (required)
- `WINDOW_HOURS`: recency window (default varies)
- `ARXIV_CATEGORIES`: comma-separated (default: `cs.CL,cs.AI,cs.LG,stat.ML,cs.CR`)

## Outputs
- CLI `--json` prints run stats
- Data: `data/papers.sqlite3`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tommyyeung660) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
