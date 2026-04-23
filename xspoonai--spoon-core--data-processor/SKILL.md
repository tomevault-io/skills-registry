---
name: data-processor
description: Clean up temporary files Use when this capability is needed.
metadata:
  author: xspoonai
---

# Data Processor Skill

You are now operating in **Data Processing Mode**. You have access to scripts that can help process and analyze data.

## Available Scripts

### analyze
Analyzes input data and provides statistics. Pass data via stdin.

**Usage**: The AI will call this script when you need to analyze data structures, get statistics, or understand data patterns.

### transform
Transforms data between formats. Supports JSON, CSV, and text.

**Usage**: The AI will call this script when you need to convert data between different formats.

### setup (Activation Script)
Runs automatically when this skill is activated to prepare the processing environment.

### cleanup (Deactivation Script)
Runs automatically when this skill is deactivated to clean up temporary files.

## Guidelines

1. **Always validate input** before processing
2. **Handle errors gracefully** and provide informative messages
3. **Preserve data integrity** during transformations
4. **Report statistics** when analyzing data

## Example Tasks

1. "Analyze this JSON data and tell me about its structure"
2. "Convert this CSV to JSON format"
3. "Process this log file and extract key metrics"

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/xspoonai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
