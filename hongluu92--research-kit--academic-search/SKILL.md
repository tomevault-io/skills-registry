---
name: academic-search
description: Techniques for querying academic databases (ArXiv, Semantic Scholar, PubMed) and code repositories (GitHub) Use when this capability is needed.
metadata:
  author: hongluu92
---

# Academic Search Skill

## Overview
This skill provides strategies and tool usage patterns for effectively searching academic literature. It focuses on precision searching to find high-quality, relevant papers.

## Search Strategies

### 1. Keyword Expansion
Start with a broad topic and generate synonyms.
- **Topic**: "Large Language Models in Healthcare"
- **Keywords**: "LLM", "Generative AI", "NLP", "Clinical Decision Support", "Electronic Health Records"

### 2. Boolean Operators
Use standard operators to refine searches:
- `AND`: narrow results (e.g., `LLM AND "clinical trial"`)
- `OR`: broaden results (e.g., `cancer OR oncology`)
- `NOT`: exclude specific terms (e.g., `virus NOT "computer virus"`)

### 3. Database Specifics

#### ArXiv (Computer Science, Physics)
- **Best for**: Preprints, cutting-edge AI research.
- **Search Tip**: Use category filters (e.g., `cat:cs.AI`, `cat:cs.CL`).
- **Sorting**: Sort by "Submitted Date" to see the absolute latest work.

#### Semantic Scholar (General Science)
- **Best for**: Finding connected papers, traversing citation graphs.
- **Search Tip**: Use "Highly Influential Citations" to find seminal papers.
- **Filtering**: Filter by "Has PDF" to ensure full-text access.

#### GitHub (Open Source Code)
- **Best for**: Finding implementation code, frameworks, tools.
- **Search Tip**: Include "implementation", "code", or specific languages (e.g., `LLM language:python`).
- **Sorting**: Sort by "stars" to find the most popular repositories.

## Usage
Use the provided Python scripts to fetch data.

### 1. ArXiv Fetcher
```bash
python .research-agent/skills/academic-search/scripts/arxiv_fetcher.py --query "Machine Learning" --max_results 5
```

### 2. Semantic Scholar Fetcher
```bash
python .research-agent/skills/academic-search/scripts/scholar_fetcher.py --query "Transformer Architecture" --limit 5
```

### 3. GitHub Fetcher
```bash
python .research-agent/skills/academic-search/scripts/github_fetcher.py --query "Research Agent" --limit 5 --sort stars
```

## Verification
- **Citation Count**: High citations usually indicate impact.
- **Venue**: Check the conference (NeurIPS, ICML) or journal impact factor.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hongluu92) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
