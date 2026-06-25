---
name: data-analysis
description: Analyze datasets and create visualizations Use when this capability is needed.
metadata:
  author: femto
---

# Data Analysis Skill

## Description
This skill helps analyze datasets and create meaningful visualizations. It can handle CSV files, perform statistical analysis, and generate various types of plots.

## Usage Instructions

When a user requests data analysis:

1. **Load the dataset**: Use pandas to read the data file
2. **Inspect the data**: Check shape, columns, data types, and basic statistics
3. **Clean the data**: Handle missing values and outliers if necessary
4. **Perform analysis**: Calculate relevant statistics based on user's question
5. **Create visualizations**: Generate appropriate plots (line, bar, scatter, etc.)
6. **Save results**: Export results and visualizations

## Available Resources

### Scripts
- **scripts/analyze.py**: Core analysis functions
  - `load_dataset(filepath)`: Load data from various formats
  - `basic_statistics(df)`: Calculate descriptive statistics
  - `detect_outliers(df, column)`: Identify outliers
  - `correlation_analysis(df)`: Compute correlations

- **scripts/visualize.py**: Visualization utilities
  - `plot_distribution(df, column)`: Create distribution plots
  - `plot_correlation_matrix(df)`: Visualize correlation heatmap
  - `plot_time_series(df, date_col, value_col)`: Time series plots
  - `save_plot(fig, filename)`: Save figure to file

### References
- **references/examples.md**: Usage examples and common patterns
- **references/best_practices.md**: Data analysis best practices

## Example Prompts

- "Analyze this CSV file and show me the trends"
- "Create a visualization of the sales data by month"
- "Find correlations in this dataset"
- "Identify outliers in the price column"
- "Generate a statistical summary of the data"

## Output Format

Analysis results should include:
1. Data overview (shape, columns, types)
2. Statistical summary
3. Key insights and findings
4. Visualizations (saved as PNG files)
5. Recommendations or next steps

## Notes

- Always inspect data before analysis
- Handle missing values appropriately
- Choose visualizations that match the data type
- Provide clear explanations of findings
- Save all outputs for user reference

---
> Source: [femto/minion](https://github.com/femto/minion) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-25 -->
