---
name: github-trends
description: Discover the most popular and rising repositories on GitHub Use when this capability is needed.
metadata:
  author: alexzuosh
---

## What I do

- Fetch **most popular** repositories based on total stars
- Identify **rising** repositories created recently that are gaining traction
- Filter by programming language (e.g., Python, Rust, JavaScript)
- Display results in a beautiful, interactive CLI table
- Show key metrics: Stars, Forks, Description, and URL

## When to use me

Use this skill when you need to:
- **Discover new libraries** and tools in a specific ecosystem
- **Track trending projects** to stay updated with the community
- **Find popular alternatives** to existing solutions
- **Explore open source** projects for contribution

## How to use me

Find the most popular repositories overall:

```
Use the github-trends skill to find popular repos
```

Find rising Python repositories created in the last month:

```
Use the github-trends skill to find rising python repos
```

Find the most popular Rust repositories:

```
Use the github-trends skill to find popular rust repos
```

## What I need

1. **Internet Connection** - Required to query the GitHub API
2. **Language** (optional) - Filter by specific programming language
3. **Category** (optional) - 'popular' (all time) or 'rising' (created recently)

## What I provide

1. **Dependency setup** - Automatically installs required packages:
   - `requests` - For API communication
   - `rich` - For beautiful terminal output

2. **Analysis**:
   - Fetches data from GitHub's REST API
   - Sorts and filters based on your criteria
   - Presents a clean, clickable table of results

## Implementation approach

```python
1. Parse user arguments (language, category)
2. Construct GitHub API query:
   - Popular: sort=stars, order=desc
   - Rising: created:>30_days_ago, sort=stars, order=desc
3. Fetch data from https://api.github.com/search/repositories
4. Display results using `rich.console.Console` and `rich.table.Table`
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/alexzuosh) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
