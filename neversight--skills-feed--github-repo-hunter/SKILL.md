---
name: github-repo-hunter
description: Autonomous GitHub repository discovery and integration agent that hunts relevant open-source projects for BidDeed.AI (foreclosure auctions) and Life OS (productivity/ADHD). Searches GitHub for repos matching domain keywords, evaluates relevance via README/tech stack analysis, auto-adds as submodules or archives to target repos, and alerts Ariel with actionable summaries. Use when (1) User requests "find relevant GitHub repos", (2) Exploring new integrations for BidDeed.AI workflows, (3) Discovering productivity tools for Life OS, (4) Keeping repositories updated with latest open-source innovations. Use when this capability is needed.
metadata:
  author: neversight
---

# GitHub Repository Hunter

Autonomous agent for discovering, evaluating, and integrating relevant GitHub repositories into BidDeed.AI and Life OS ecosystems.

## Mission

Hunt GitHub for open-source projects that could enhance:
1. **BidDeed.AI** - Foreclosure auction intelligence (scrapers, ML models, document processing, workflow automation)
2. **Life OS** - Productivity, ADHD management, swimming analytics, dual-timezone coordination

## Core Workflow

### 1. Discovery Phase
Search GitHub API using domain-specific keywords.

Use `scripts/hunt_repos.py` with keywords from `references/keyword_library.md`.

### 2. Evaluation Phase
Score each discovered repo (1-100) using `scripts/evaluate_repo.py`.

**Quick Filters (Auto-reject):**
- Last commit >1 year ago
- <10 stars AND <5 forks  
- No README
- Archived repository

**Scoring Rubric:**
- Tech Stack Match (30 pts) - Python/Rust/JS, Supabase, LangGraph, GitHub Actions
- Domain Relevance (40 pts) - Foreclosure tools, ADHD/productivity, swimming analytics
- Code Quality (20 pts) - Tests, docs, active issues
- Community (10 pts) - Stars, forks, contributors

**Thresholds:**
- Score ≥70 → AUTO_ADD to integrations/
- Score 50-69 → ALERT_ARIEL (needs review)
- Score <50 → SKIP (log to rejected_repos.txt)

### 3. Integration Phase
Execute `scripts/integrate_repo.py` for AUTO_ADD repos.

**Integration Methods:**
A. Git submodule in integrations/ folder
B. Reference entry in docs/integrations.md

### 4. Alert Phase
Insert discovery notification to Supabase insights table.

**Alert Format:**
```
🔍 GitHub Repo Hunter - Found: {repo_name}

Score: {score}/100 (AUTO_ADD / REVIEW_NEEDED)
Repository: https://github.com/{user}/{repo}
Language: {languages}
Last Updated: {last_commit}
Stats: ⭐ {stars} | 🍴 {forks} | 👥 {contributors}

What It Does:
{readme_summary}

Potential Use Cases:
- BidDeed.AI: {use_case}
- Life OS: {use_case}

Tech Stack Match:
✅ {matched_tech}
❌ {missing_tech}

Recommendation: {action}
Next Steps: {specific_action}
```

## Scripts

`scripts/hunt_repos.py` - Main hunter with GitHub API
`scripts/evaluate_repo.py` - Scoring algorithm
`scripts/integrate_repo.py` - Auto-add to repos

## References

`references/keyword_library.md` - Search keywords for both domains
`references/github_api.md` - GitHub Search API docs
`references/scoring_rubric.md` - Detailed evaluation criteria

## Critical Rules

1. Never auto-add without evaluation
2. Respect GitHub API rate limits (5000 req/hr with auth)
3. Test before integrating
4. Document every integration in docs/integrations.md
5. Alert on threshold (50-69 MUST notify Ariel)

## Target Repositories

**Primary:**
- `breverdbidder/biddeed-conversational-ai`
- `breverdbidder/life-os`

**Secondary:**
- `breverdbidder/brevard-bidder-scraper` (archive-only)

## Decision Tree

```
Discovered Repo
    |
    ├─ Score ≥70? → Auto-add + Alert (FYI)
    ├─ Score 50-69? → Supabase alert + Review needed
    └─ Score <50? → Skip (log rejection)
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
