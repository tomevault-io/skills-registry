---
name: github-stars-collector
description: Collect GitHub starred repositories and save them as structured Markdown with AI-generated summaries. Use when user wants to: (1) Initialize their GitHub Stars collection, (2) Update collection with newly starred repos, (3) Build a searchable database of their starred repositories. Supports batch processing for large collections (1000+ repos). Incremental updates only - only processes newly starred repositories. Use when this capability is needed.
metadata:
  author: z0gsh1u
---

Collect and manage your GitHub Stars collection with AI-generated summaries.

## Prerequisites

1. **Install Python dependencies**
   ```bash
   pip install PyGithub
   ```

2. **Configure GitHub Token**
   - Create Personal Access Token: GitHub Settings > Developer settings > Personal access tokens
   - Required permission: `public_repo`
   - Add token to `.claude/skills/data/.env`:
     ```
      GITHUB_TOKEN=ghp_your_token_here
      ```

## Workflow

Process repositories in batches to avoid context limits.

### Step 1: Fetch batch
```bash
python .claude/skills/github-stars-collector/scripts/fetch_stars.py --limit 5 --offset 0
```

Returns JSON with repositories and README content:
```json
{
  "existing_count": 0,
  "new_count": 1000,
  "limit": 5,
  "offset": 0,
  "has_more": true,
  "new_repos": [
    {
       "full_name": "owner/repo-name",
       "description": "Repository description",
       "language": "Python",
       "stars": 1200,
       "url": "https://github.com/owner/repo-name",
       "topics": ["web", "api"],
       "readme_content": "Full README text..."
    }
  ]
}
```

### Step 2: Generate summaries
For each repository in `new_repos`:
- Read `readme_content`
- Generate `function_description`: concise description of core functionality
- Generate `use_cases`: list of 2-3 specific use cases

### Step 3: Pipe JSON to save_repos.py
```bash
echo '{
  "repositories": [
    {
      "full_name": "owner/repo-name",
      "description": "Repository description",
      "language": "Python",
      "stars": 1200,
      "url": "https://github.com/owner/repo-name",
      "topics": ["web", "api"],
      "function_description": "Core functionality description",
      "use_cases": [
        "Scenario 1: specific use case",
        "Scenario 2: specific use case"
      ]
    }
  ]
}' | python .claude/skills/github-stars-collector/scripts/save_repos.py
```

Success response:
```json
{
  "status": "success",
  "added": 5,
  "total": 5,
  "file": "/path/to/.claude/skills/data/github-stars.md"
}
```

### Step 4: Repeat for next batch
If `has_more: true`:
```bash
# Next offset = previous offset + limit
python .claude/skills/github-stars-collector/scripts/fetch_stars.py --limit 5 --offset 5
```
- Repeat Step 2 and Step 3
- Continue until `has_more: false`

### Error Handling
If validation fails, `save_repos.py` returns detailed errors:
```json
{
  "status": "validation_failed",
  "message": "Failed to validate 1 repository(ies). Fix the errors and try again.",
  "errors": [
    {
      "index": 0,
      "repo": "owner/repo-name",
      "error": "Missing or empty field: function_description"
    }
  ],
  "valid_count": 4,
  "error_count": 1
}
```
Fix the errors in the JSON and retry the `echo '{...}' | save_repos.py` command.

## Parameters

**fetch_stars.py**:
- `--limit N`: Number of repos per batch (default: 5)
- `--offset N`: Skip first N new repos (default: 0)

**save_repos.py**:
- Reads JSON from stdin (no parameters)

**Complete example:**
```bash
# Batch 1: repos 0-4
python .claude/skills/github-stars-collector/scripts/fetch_stars.py --limit 5 --offset 0
# [AI processes and generates JSON]
echo '{"repositories": [...]}' | python .claude/skills/github-stars-collector/scripts/save_repos.py

# Batch 2: repos 5-9
python .claude/skills/github-stars-collector/scripts/fetch_stars.py --limit 5 --offset 5
# [AI processes and generates JSON]
echo '{"repositories": [...]}' | python .claude/skills/github-stars-collector/scripts/save_repos.py

# Continue until has_more: false
```

## Data Format

Each repository entry in `github-stars.md`:

```markdown
<!-- REPO: owner/repo-name -->
## owner/repo-name

**Description:** Repository description

**Basic Info:**
- Language: Python
- Stars: 1.2k
- URL: https://github.com/owner/repo-name
- Topics: web, api, graphql

**README Summary:**

### Function Description
[Concise description of core functionality]

### Use Cases
- Scenario 1: specific use case
- Scenario 2: specific use case

---
```

## Error Handling

**fetch_stars.py errors:**
- `PyGithub not installed`: Run `pip install PyGithub`
- `GITHUB_TOKEN not found in .env file`: Add token to `.claude/skills/data/.env`
- `Failed to connect to GitHub`: Check token validity and network
- `Failed to fetch starred repos`: Check API rate limits, wait and retry

**save_repos.py errors:**
- `No JSON input received from stdin`: Pipe JSON data using `echo '{...}' | save_repos.py`
- `Invalid JSON input`: Check JSON syntax
- `Input JSON must contain 'repositories' array`: Ensure top-level key is `repositories`
- `Missing or empty field`: Add required fields to each repo entry
- `Field 'use_cases' must be a list`: Ensure `use_cases` is an array, not a string
- `Field 'use_cases' list cannot be empty`: Add at least one use case
- `Repository already exists in github-stars.md`: Skip or delete existing entry

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/z0gsh1u) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
