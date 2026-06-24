---
name: rize
description: | Use when this capability is needed.
metadata:
  author: GoBeromsu
---

# Rize API Helper

## Overview
Act as intelligent wrapper for Rize's GraphQL API.
Interpret user requests, map to appropriate API queries, fetch data,
and save to user-specified location in vault.

Uses `scripts/fetch_rize_data.sh` to handle all API communication and data formatting.

## When to Use
- When user invokes: `/rize [request]`
- To fetch focus time, categories, breakdowns, or trends from Rize
- To analyze productivity patterns from Rize tracking
- As a conversational API interface for Rize data

## Setup

### 1. Configure API Key

The API key is stored in `.env` file in the skill directory.

**First time setup:**
```bash
# Copy example file
cp .claude/skills/rize/.env.example .claude/skills/rize/.env

# Edit .env and add your API key
# Get it from Rize app: Settings > API
```

The `.env` file should contain:
```bash
RIZE_API_KEY=your_actual_api_key_here
```

**Security:** The `.env` file is in `.gitignore` and will not be committed to version control.

### 2. Verify Dependencies

The script requires:
- `curl` - For API requests
- `jq` - For JSON parsing
- `bc` - For calculations

Install missing dependencies:
```bash
brew install curl jq bc
```

## Request Patterns

Interpret natural language requests:

### Supported Request Types

1. **Weekly/Daily Summaries**
   - "Get my focus time for this week"
   - "Show daily breakdown for current week"
   - "What's my total focus time this month?"

2. **Category Analysis**
   - "Break down focus time by category this week"
   - "What are my top 3 focus categories?"
   - "Show all categories with time spent"

3. **Comprehensive Reports**
   - "Give me a complete weekly report"
   - "Show everything for this week: focus, categories, sessions"

4. **Custom Output Location**
   - "Weekly report → \"50. AI/Logs/Rize/2026-01.md\""
   - "Focus time save to \"My File.md\""

## Workflow

When user invokes `/rize [request]`:

### Step 1: Parse Request
Extract from user's natural language request:
- **Time period**: week, month, today (defaults to week)
- **Include categories**: if "category" mentioned
- **Output location**: look for "→" or "save to" with path

### Step 2: Execute Script
Run the fetch script with the user's request:

```bash
/Users/beomsu/Documents/Obsidian/Ataraxia/.claude/skills/rize/scripts/fetch_rize_data.sh "Get my weekly focus time by category"
```

Script execution:
1. Load API key from `.env`
2. Parse the request
3. Calculate date range (current week/month/today)
4. Build GraphQL query
5. Execute API request
6. Format response as Obsidian markdown
7. Save to file or output to stdout

### Step 3: Handle Output
- If user specified output path → Save to that file
- Otherwise → Display markdown and optionally save to default location

## Script Features

`fetch_rize_data.sh` script provides:

### Automatic Date Calculation
- "this week" → Current Monday-Sunday
- "this month" → Current month start-end
- "today" → Today's date

Works on both macOS and Linux.

### Smart Request Parsing
Detects keywords:
- "week/weekly" → Week period
- "month/monthly" → Month period
- "today" → Today
- "category/categories" → Include category breakdown

### Data Formatting
Outputs clean Obsidian markdown with:
- YAML frontmatter (type, date_created, source_url)
- Summary metrics (focus, tracked, break, meeting time)
- Category breakdown table (if requested)
- Daily breakdown table
- Insights callout with top category

### Error Handling
- Missing .env file → Setup instructions
- Missing API key → Configuration guidance
- API errors → Display error message
- No data → Warning message
- Missing dependencies → Installation instructions

## Example Invocations

```
/rize Get my focus time for this week
/rize Break down this week by category
/rize Monthly report for January
/rize from 2025-01-01 to 2025-12-31 with categories
```

## Output Format

All outputs include:
1. YAML frontmatter (type, date_created, source_url)
2. Summary metrics (focus, tracked, break, meeting times)
3. Category breakdown table (if requested)
4. Daily/weekly/monthly breakdown table
5. Insights callout with top category

## Troubleshooting

### Error: ".env file not found"
```bash
# Copy the example file
cp .claude/skills/rize/.env.example .claude/skills/rize/.env

# Edit and add your API key
```

### Error: "RIZE_API_KEY not set in .env"
```bash
# Edit .env file
nano .claude/skills/rize/.env

# Add:
RIZE_API_KEY=your_actual_api_key
```

### Error: "Rize API Error: Authentication Error"
- Check API key is correct in `.env`
- Verify key is still valid in Rize app (Settings > API)
- Try regenerating key if needed

### Error: "No focus time data found"
- Verify Rize is running and tracking
- Check the date range has data in Rize app
- Try a different time period

### Error: "command not found: jq"
```bash
# Install dependencies
brew install jq bc
```

### Error: Script not executable
```bash
chmod +x .claude/skills/rize/scripts/fetch_rize_data.sh
```

## File Structure

```
.claude/skills/rize/
├── SKILL.md                         # This file
├── .env                             # API key (not in git)
├── .env.example                     # Template
├── .gitignore                       # Ignores .env
├── scripts/
│   ├── fetch_rize_data.sh           # Main script
│   ├── lib/
│   │   ├── api.sh                   # curl wrapper
│   │   ├── date_utils.sh            # Date helpers
│   │   └── format.sh                # Markdown formatter
│   └── templates/
│       ├── summaries.graphql        # Main query
│       ├── categories.graphql       # Alternative endpoint
│       └── sessions.graphql         # Future
└── references/
    └── api.md                       # API documentation
```

## References

For detailed API documentation, query parameters, timeout handling, and limitations, see `references/api.md`.

---
> Source: [GoBeromsu/claude-plugins](https://github.com/GoBeromsu/claude-plugins) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-22 -->
