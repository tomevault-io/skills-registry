---
name: sql-analysis
description: Guided workflow for SQL data analysis using db_tools Use when this capability is needed.
metadata:
  author: datus-ai
---

# SQL Analysis Workflow

This skill guides you through a structured data analysis workflow using database tools.

## Workflow Steps

### Step 1: Schema Discovery
First, understand the database schema:
```
Use db_tools.list_tables() to see available tables
Use db_tools.describe_table(table_name) for each relevant table
```

### Step 2: Data Exploration
Explore the data with sample queries:
```
Use db_tools.execute_sql("SELECT * FROM {table} LIMIT 10")
```

### Step 3: Analysis Query
Based on the user's question, construct and execute the analysis query.

### Step 4: Result Interpretation
Analyze the results and provide insights.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/datus-ai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
