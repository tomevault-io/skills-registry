---
name: creating-db-analytics-analysis
description: Use when creating new multi-subscriber database analyses in db-analytics project - guides the complete workflow from database exploration through CI integration, using subagents for each step and ensuring all changes are on a feature branch
metadata:
  author: nagyv
---

# Creating DB Analytics Analysis

## Overview

This skill guides creating new database analyses. Each analysis follows a structured pipeline: explore schema → collect data → analyze cross-subscriber → create per-subscriber reports → integrate with CI.

**All changes MUST be made on a feature branch.**

## When to Use

- User asks to "create a new analysis" or "analyze {topic}"
- User wants to understand a new domain in the clearvisio database
- User needs cross-subscriber insights on a topic

## Prerequisites

Before starting, verify:

1. **Git status is clean**: Run `git status`. If uncommitted changes exist, ask Viktor how to handle them before proceeding.
2. **database_keys.parquet exists**: Check `data/raw/database_keys.parquet` exists (needed for multi-subscriber analysis).

## Naming Convention

Convert topic to slug format for filenames:
- "Returning Customers" → `returning_customers`
- "SMS Patterns" → `sms_patterns`
- Use lowercase with underscores, no spaces or special characters

## Step 0: Ask Questions and Create Todos (MANDATORY)

**You MUST complete this step before any other work.**

### 0.1 Ask Initial Questions

Use AskUserQuestion to gather:

1. **Topic**: What domain/topic to analyze? (e.g., "returning customers", "SMS patterns")
2. **Template**: Do you need per-subscriber reports? (yes/no)
3. **Key questions**: What specific questions should the analysis answer?

### 0.2 Create TodoWrite Todos

After receiving answers, IMMEDIATELY create todos:

```
TodoWrite with these items:
- [ ] Create feature branch for {topic}
- [ ] Explore database schema (subagent)
- [ ] Create data collection notebook (subagent)
- [ ] Verify data collection notebook runs
- [ ] Push the branch to GitLab and create MR
- [ ] Ask the user to review the data collection notebook
- [ ] Create panel analysis notebook (subagent)
- [ ] Verify panel analysis notebook runs
- [ ] Create template notebook (subagent) [if needed]
- [ ] Verify template notebook runs [if needed]
- [ ] Add CI jobs (subagent)
- [ ] Final review and commit
```

## Step 1: Create Feature Branch

**Before any file changes**, create a feature branch:

```bash
git checkout -b analysis/{topic_slug}
```

Example: `git checkout -b analysis/returning_customers`

**Success criteria:** Branch created, `git branch` shows you're on the new branch.

## Step 2: Database Exploration (Subagent)

Dispatch a subagent using the Task tool with `subagent_type: "general-purpose"`:

**Prompt:**
```
Explore the clearvisio database schema to understand tables and relationships for analyzing {TOPIC}.

Working directory: /Users/nagyv/Projects/Clearvisio/db-analytics

Tasks:
1. Connect to the clearvisio database using:
   ```python
   from src.database.connector import get_engine
   engine = get_engine('clearvisio')
   ```
2. Identify relevant tables using INFORMATION_SCHEMA:
   ```sql
   SELECT TABLE_NAME FROM INFORMATION_SCHEMA.TABLES
   WHERE TABLE_SCHEMA = 'clearvisio' AND TABLE_NAME LIKE '{prefix}_%'
   ```
3. Examine table structures with DESCRIBE
4. Find foreign key relationships via KEY_COLUMN_USAGE

Output:
- Describe the database structure identified in `docs/db-structure/{TOPIC}.md`
- List of relevant tables with their purposes
- Key columns for the analysis
- Sample SQL query that will work across subscriber databases
- Any data quality concerns
```

**Success criteria:** You have a clear understanding of which tables and columns to query, and you documented your findings in a Markdown file.

**Error handling:** If no relevant tables found, ask Viktor to clarify the topic or suggest alternative approaches.

## Step 3: Data Collection Notebook (Subagent)

Dispatch a subagent to create `notebooks/exploratory/multi_subscriber_{topic_slug}.ipynb`:

**Prompt:**
```
Create a multi-subscriber data collection notebook.

Working directory: /Users/nagyv/Projects/Clearvisio/db-analytics
Output file: notebooks/exploratory/multi_subscriber_{TOPIC_SLUG}.ipynb

The notebook MUST have these cells in order:

CELL 1 (Markdown):
# Multi-Subscriber {Topic} Analysis
Brief description of what this notebook analyzes.

CELL 2 (Code - Imports):
import sys
sys.path.append('../..')

import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns
from src.database.connector import subscriber_database
from src.data.multi_subscriber import analyze_all_subscribers, load_subscribers

pd.set_option('display.max_columns', None)
sns.set_style('whitegrid')

CELL 3 (Markdown):
## Analysis Function

CELL 4 (Code - Analysis function):
def analyze_{topic_slug}(engine, subscriber_name):
    """Analyze {topic} for a subscriber database.

    Args:
        engine: SQLAlchemy engine connected to subscriber database
        subscriber_name: Name of the subscriber

    Returns:
        dict with metrics and optional 'data' DataFrame
    """
    query = '''
    {SQL_QUERY_FROM_EXPLORATION}
    '''

    try:
        df = pd.read_sql(query, engine)

        if len(df) == 0:
            return {{'error': 'No data found'}}

        return {{
            'metric1': value1,
            'data': df,
            'error': None
        }}
    except Exception as e:
        return {{'error': str(e)}}

CELL 5 (Markdown):
## Test with Small Subset

CELL 6 (Code - Test):
test_results = analyze_all_subscribers(analyze_{topic_slug}, limit=5)
# Display results
pd.DataFrame.from_dict(
    {{k: {{key: val for key, val in v.items() if key != 'data'}}
     for k, v in test_results.items()}},
    orient='index'
)

CELL 7 (Markdown):
## Run Across All Subscribers

CELL 8 (Code - Full run):
all_results = analyze_all_subscribers(analyze_{topic_slug})

CELL 9 (Markdown):
## Aggregate and Save

CELL 10 (Code - Save):
import os
os.makedirs('../../data/interim', exist_ok=True)

# Combine all data
all_data = []
for subscriber, result in all_results.items():
    if result.get('error') is None and result.get('data') is not None:
        df = result['data'].copy()
        df['subscriber'] = subscriber
        all_data.append(df)

combined_df = pd.concat(all_data, ignore_index=True)
combined_df.to_parquet('../../data/interim/{topic_slug}.parquet', index=False)
print(f"Saved {{len(combined_df)}} rows to data/interim/{topic_slug}.parquet")

Tables to use: {TABLES_FROM_STEP_2}
Key questions: {KEY_QUESTIONS}
```

**Success criteria:** Notebook created, runs without errors with limit=5.

**Verification:** After subagent completes, run the notebook locally:
```bash
uv run jupyter nbconvert --to notebook --execute notebooks/exploratory/multi_subscriber_{topic_slug}.ipynb --output /tmp/test.ipynb
```

**Error handling:** If some subscribers fail, that's expected. Check that most succeed and errors are properly captured.

## Step 4: Part-time Review and Commit

After subagents complete:

3. **Commit with descriptive message:**
   ```bash
   git add notebooks/exploratory/multi_subscriber_{topic_slug}.ipynb
   git commit -m "Add {topic} analysis data collection"
   ```

4. **Push for review:**
   ```bash
   git push -u origin analysis/{topic_slug}
   ```

**Success criteria:** All files committed, branch pushed, ready for user review before continuing.

## Step 5: Panel Analysis Notebook (Subagent)

Dispatch a subagent to create `notebooks/panel/{topic_slug}_analysis.ipynb`:

**Prompt:**
```
Create a panel analysis notebook for cross-subscriber {TOPIC} analysis.

Working directory: /Users/nagyv/Projects/Clearvisio/db-analytics
Output file: notebooks/panel/{TOPIC_SLUG}_analysis.ipynb
Input data: data/interim/{TOPIC_SLUG}.parquet

The notebook MUST:

1. Load data:
import sys
sys.path.append('../..')

import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
from sklearn.cluster import KMeans  # if clustering needed

df = pd.read_parquet('../../data/interim/{topic_slug}.parquet')

2. Include sections for:
   - Data overview and summary statistics
   - Distribution visualizations (histograms, box plots)
   - Cross-subscriber comparisons
   - Clustering analysis (if appropriate)
   - Outlier identification
   - Key insights summary

3. Answer these questions: {KEY_QUESTIONS}

4. Use clear titles, labels, and formatting for all visualizations.
```

**Success criteria:** Notebook created, loads parquet file, produces visualizations.

**Verification:** Run the notebook:
```bash
uv run jupyter nbconvert --to notebook --execute notebooks/panel/{topic_slug}_analysis.ipynb --output /tmp/test.ipynb
```

## Step 6: Template Notebook - Optional (Subagent)

**Only create if user requested per-subscriber reports.**

Dispatch a subagent to create `notebooks/templates/{topic_slug}.ipynb`:

**Prompt:**
```
Create a per-subscriber template notebook for {TOPIC} analysis.

Working directory: /Users/nagyv/Projects/Clearvisio/db-analytics
Output file: notebooks/templates/{TOPIC_SLUG}.ipynb
Input data: data/interim/{TOPIC_SLUG}.parquet

CRITICAL REQUIREMENT: First code cell MUST be tagged 'parameters' and contain:
# parameters for papermill, default values
subscriber_name = "default_subscriber"

The notebook MUST:

1. Print subscriber name header:
print(f"# {TOPIC} analysis for `{{subscriber_name}}`")

2. Load and filter data:
import sys
sys.path.append('../..')

import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
from pathlib import Path
from src.database.connector import get_project_root

parquet_path = get_project_root() / 'data/interim/{topic_slug}.parquet'
all_df = pd.read_parquet(parquet_path)
current_df = all_df[all_df['subscriber'] == subscriber_name].copy()

3. Provide subscriber-specific analysis:
   - Key metrics for this subscriber
   - Visualizations
   - Comparison to overall averages
   - Summary findings

Run command: python main.py notebooks/templates/{topic_slug}.ipynb
```

**Success criteria:** Notebook created with parameters cell, runs for a single subscriber.

**Verification:**
```bash
uv run python main.py notebooks/templates/{topic_slug}.ipynb --limit 1
```

## Step 7: CI Integration (Subagent)

Dispatch a subagent to update `.gitlab/ci/analytics.yml`:

**Prompt:**
```
Add CI jobs to .gitlab/ci/analytics.yml for {TOPIC} analysis.

Working directory: /Users/nagyv/Projects/Clearvisio/db-analytics
File to edit: .gitlab/ci/analytics.yml

Add these jobs:

1. Data collection job in 'analyze' stage:
load_{topic_slug}:
  extends: .notebook_base
  stage: analyze
  needs: ["generate_database_keys"]
  script:
    - uv run jupyter nbconvert --to notebook --execute --inplace notebooks/exploratory/multi_subscriber_{topic_slug}.ipynb
  artifacts:
    paths:
      - data/interim/{topic_slug}.parquet
      - notebooks/exploratory/multi_subscriber_{topic_slug}.ipynb
    expire_in: 30 days

2. Panel analysis job in 'templates' stage:
analyse_{topic_slug}:
  extends: .notebook_base
  stage: templates
  needs:
    - generate_database_keys
    - load_{topic_slug}
  script:
    - uv run jupyter nbconvert --to notebook --execute --inplace notebooks/panel/{topic_slug}_analysis.ipynb
  artifacts:
    paths:
      - notebooks/panel/{topic_slug}_analysis.ipynb
    expire_in: 30 days

3. If template notebook exists, add to run_template job's script:
    - uv run python main.py notebooks/templates/{topic_slug}.ipynb

Also update run_template's 'needs' to include load_{topic_slug} if template uses that data.
```

**Success criteria:** CI jobs added with correct stages and dependencies.

## Step 8: Final Review and Commit

After all subagents complete:

1. **Review created files:**
   - `notebooks/exploratory/multi_subscriber_{topic_slug}.ipynb`
   - `notebooks/panel/{topic_slug}_analysis.ipynb`
   - `notebooks/templates/{topic_slug}.ipynb` (if created)
   - `.gitlab/ci/analytics.yml`

2. **Verify notebooks run locally** (if not already done in verification steps)

3. **Commit with descriptive message:**
   ```bash
   git add notebooks/exploratory/multi_subscriber_{topic_slug}.ipynb
   git add notebooks/panel/{topic_slug}_analysis.ipynb
   git add notebooks/templates/{topic_slug}.ipynb  # if exists
   git add .gitlab/ci/analytics.yml
   git commit -m "Add {topic} analysis notebooks and CI integration"
   ```

4. **Push for final review:**
   ```bash
   git push -u origin analysis/{topic_slug}
   ```

**Success criteria:** All files committed, branch pushed, ready for merge request.

## Common Mistakes

| Mistake | Correct Approach |
|---------|------------------|
| Skipping database exploration | ALWAYS explore schema first - you need to know the tables |
| Wrong notebook location | exploratory → `notebooks/exploratory/`, panel → `notebooks/panel/`, templates → `notebooks/templates/` |
| Missing parameters cell in template | FIRST code cell MUST have `subscriber_name = "default_subscriber"` |
| Not testing with limit=5 | ALWAYS test with limit=5 before running on all subscribers |
| Wrong parquet path | From notebooks: `../../data/interim/{topic}.parquet`. From templates: use `get_project_root()` |
| Missing CI integration | ALWAYS add jobs to analytics.yml |
| Working on main branch | ALWAYS create feature branch first |
| Not creating todos | ALWAYS create TodoWrite todos at the start |
| Skipping verification | ALWAYS verify each notebook runs before proceeding |

## Troubleshooting

| Problem | Solution |
|---------|----------|
| Some subscribers fail in analysis | Expected - check error rate, if >10% fail investigate |
| Parquet file not created | Check notebook ran fully, check path is correct |
| Template notebook can't find subscriber | Verify subscriber exists in parquet file's 'subscriber' column |
| CI job fails | Check dependencies in 'needs', verify artifact paths |

## Checklist (Create as TodoWrite todos)

- [ ] Asked initial questions (topic, template needed, key questions)
- [ ] Created feature branch
- [ ] Explored database schema (subagent)
- [ ] Documented database schema structure (subagent)
- [ ] Created data collection notebook (subagent)
- [ ] Verified data collection notebook runs
- [ ] Created panel analysis notebook (subagent)
- [ ] Verified panel analysis notebook runs
- [ ] Created template notebook if needed (subagent)
- [ ] Verified template notebook runs (if created)
- [ ] Added CI jobs (subagent)
- [ ] Final review and commit
- [ ] Branch pushed for review

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/nagyv) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
