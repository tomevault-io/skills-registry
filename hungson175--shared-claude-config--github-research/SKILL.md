---
name: github-research
description: Find reputable GitHub repositories with quality filters (stars, recency, license). Use this skill when the user needs to find code samples, reference implementations, libraries, or templates on GitHub. Triggers on requests like "find GitHub repos for X", "search GitHub for Y examples", "find a good library for Z", or any request to discover quality open-source code. Requires gh CLI (version 2.14+). Use when this capability is needed.
metadata:
  author: hungson175
---

# GitHub Research

Find reputable GitHub repositories using quality filters. Prioritize curated Awesome Lists, then search with star thresholds to ensure code quality.

## Workflow

### Step 1: Check for Awesome Lists First

Awesome Lists are community-curated collections of quality resources. Search them first:

```bash
gh search repos "awesome <topic>" --stars=">=5000" --limit=5 --json name,stargazersCount,url,description
```

If a relevant Awesome List exists (e.g., `awesome-python`, `awesome-react`), recommend it as the primary source.

### Step 2: Search Repositories with Quality Filters

```bash
gh search repos "<query>" \
  --language=<lang> \
  --stars=">=1000" \
  --archived=false \
  --limit=10 \
  --json name,url,stargazersCount,license,description,pushedAt
```

**Default quality threshold:** 1000+ stars. Adjust based on user needs:
- Production code: `--stars=">=1000"`
- Learning/examples: `--stars=">=500"`
- Exploration: `--stars=">=100"`

### Step 3: Additional Filters (as needed)

| Filter | Flag | Example |
|--------|------|---------|
| Topic | `--topic` | `--topic=cli` |
| Recency | `--updated` | `--updated=">2024-01-01"` |
| License | `--license` | `--license=mit` |
| Owner | `--owner` | `--owner=microsoft` |
| Sort | `--sort` | `--sort=stars` |

### Step 4: Validate Top Results

For the top 3-5 candidates, check quality indicators:

```bash
gh api "repos/<owner>/<repo>" --jq '{
  stars: .stargazers_count,
  forks: .forks_count,
  issues: .open_issues_count,
  updated: .pushed_at,
  license: .license.spdx_id
}'
```

**Quality indicators:**
- Recent activity (updated within 6 months)
- Active issues/PRs (maintained)
- Multiple contributors
- Clear license (MIT, Apache-2.0 preferred)

### Step 5: Save Results

Save research to `docs/research/` as Markdown:

```
docs/research/YYYY-MM-DD-<topic>-github-repos.md
```

Include: repo name, stars, license, URL, and brief description.

## Examples

**Find FastAPI templates:**
```bash
gh search repos "fastapi template" --language=python --stars=">=500" --archived=false --limit=10
```

**Find CLI tools in Go:**
```bash
gh search repos --topic=cli --language=go --stars=">=1000" --limit=10
```

**Find React component libraries:**
```bash
gh search repos "react component library" --language=typescript --stars=">=1000" --updated=">2024-01-01"
```

## Rate Limits

- Search API: 30 requests/minute (authenticated)
- Check remaining: `gh api rate_limit --jq '.resources.search.remaining'`

## Prerequisites

Requires GitHub CLI version 2.14+ with `gh search repos` command.
Check version: `gh version`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hungson175) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
