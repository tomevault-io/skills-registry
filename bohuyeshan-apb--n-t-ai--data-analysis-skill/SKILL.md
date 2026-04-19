---
name: data-analysis
description: Use when working with a skill for analyzing data using Python (pandas) and generating professional visualizations (matplotlib/seaborn).
metadata:
  author: bohuyeshan-apb
---

# Data Analysis & Visualization Skill

This skill empowers the agent to perform data analysis and generate visualizations using Python libraries. It is designed to be used in conjunction with a sandbox environment.

## Capabilities

### 1. Data Processing
- **Pandas**: Clean, filter, and aggregate data.
- **NumPy**: Numerical computations.

### 2. Visualization
- **Matplotlib/Seaborn**: Generate high-quality charts (bar, line, scatter, pie, heatmap).
- **Theme Matching**: Apply color palettes consistent with the document theme.

## Usage

The skill provides utility functions to generate Python code that can be executed in the sandbox.

### Scripts
- `code_generator.py`: Generates Python scripts for analysis and plotting.
- `visualizer.py`: Helper library (injected into sandbox) to ensure consistent styling.

## Dependencies (Sandbox Environment)
- pandas
- numpy
- matplotlib
- seaborn

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/bohuyeshan-apb) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
