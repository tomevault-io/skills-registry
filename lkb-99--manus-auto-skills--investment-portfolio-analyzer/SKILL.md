---
name: investment-portfolio-analyzer
description: "Use this skill for in-depth analysis and management of investment portfolios using key risk and performance metrics. Triggers: investment portfolio, portfolio analysis, risk management, asset allocation, stock analysis, investment returns, portfolio optimization, analyze my investments, carteira de investimentos, análise de portfólio."
allowed-tools: [Read, Write, Edit, Bash, Browser]
license: MIT License
metadata:
    skill-author: Lucas Kefler Bergamaschi
---

# Investment Portfolio Analyzer

## Overview
This skill provides a comprehensive and robust toolkit for analyzing, managing, and optimizing investment portfolios. It leverages a wide range of key financial metrics and models to assess performance, evaluate risk, and empower users to make data-driven, informed investment decisions. The skill is meticulously designed for a broad audience, from individual investors seeking to better understand their holdings to seasoned financial professionals requiring sophisticated analytical tools for client reporting and strategy development. By providing a structured workflow and clear explanations, this skill demystifies complex financial analysis, making it accessible and actionable.

## Automatic Triggers

**ALWAYS activate this skill when user mentions:**
- Keywords: investment portfolio, portfolio analysis, risk management, asset allocation, stock analysis, investment returns, portfolio optimization, analyze my investments, carteira de investimentos, análise de portfólio, analisar meus investimentos, otimização de carteira.
- Phrases: "analyze my investment portfolio", "how are my investments performing?", "optimize my portfolio", "should I rebalance my assets?", "analisar minha carteira de investimentos", "como estão meus investimentos?", "otimizar minha carteira".
- Context: Any discussion about analyzing, managing, or optimizing a collection of financial assets.

**Example user queries that trigger this skill:**
- "Can you analyze my stock portfolio?"
- "I need a detailed risk assessment of my investments."
- "Quero otimizar minha carteira de investimentos para um maior retorno."
- "Gostaria de uma análise completa do meu portfólio de ações."

## When to Use This Skill
This skill is particularly useful and recommended in the following scenarios:

*   **Routine Portfolio Health Checks:** Conduct periodic and thorough reviews of your portfolio's performance, risk exposure, and overall health. This is essential for long-term investment success.
*   **Due Diligence on New Investments:** Rigorously assess the potential impact of adding a new asset or security to your existing portfolio. This helps in making calculated investment choices.
*   **Comparative Analysis of Investment Strategies:** Systematically analyze and compare different asset allocation models, rebalancing strategies, and their potential long-term outcomes. This allows for strategic adjustments and optimization.
*   **Comprehensive Risk Management:** Proactively identify, quantify, and mitigate potential risks within your portfolio, including market risk, credit risk, and liquidity risk. A robust risk management process is crucial for preserving capital.
*   **Client and Stakeholder Reporting:** Generate detailed, professional, and easy-to-understand reports for clients, stakeholders, or for personal record-keeping and tax purposes. Clear communication is key to maintaining trust and confidence.
*   **Academic and Research Purposes:** Utilize the skill's capabilities for academic research, financial modeling, and backtesting of investment theories and strategies. The skill provides a practical framework for empirical finance studies.
*   **Scenario Analysis and Stress Testing:** Evaluate how your portfolio might perform under various adverse market conditions, helping you to build a more resilient investment strategy.

## Getting Started

Before you can use this skill, you need to have your portfolio data ready. The skill expects a CSV file with historical data for your assets and a JSON file that defines your portfolio's composition.

### Data Requirements

*   **Historical Data (CSV):** This file should contain the historical price and dividend data for each asset in your portfolio. The expected columns are `Date`, `Ticker`, `Adj Close`, `Volume`, and `Dividends`. You can obtain this data from various financial data providers like Yahoo Finance, Alpha Vantage, or Quandl.
*   **Portfolio Definition (JSON):** This file, named `portfolio.json`, defines the structure of your portfolio. It includes the name of the portfolio, the benchmark you want to compare it against, the risk-free rate, and the list of assets with their respective tickers and weights.

### Example Data Files

*   **`portfolio_data.csv`**

```csv
Date,Ticker,Adj Close,Volume,Dividends
2023-01-02,AAPL,125.07,112117500,0.0
2023-01-03,AAPL,125.02,127919900,0.0
...
```

*   **`portfolio.json`**

```json
{
  "name": "My Tech Portfolio",
  "benchmark": "^IXIC",
  "risk_free_rate": 0.02,
  "assets": [
    {"ticker": "AAPL", "weight": 0.4},
    {"ticker": "GOOGL", "weight": 0.3},
    {"ticker": "MSFT", "weight": 0.3}
  ]
}
```

## Core Capabilities

### 1. Performance Analysis
This capability focuses on the granular measurement and evaluation of a portfolio's historical and risk-adjusted performance.

*   **Total Return Calculation:** Accurately calculate the total return of the portfolio over any specified period, including capital gains and dividend income. This is the fundamental measure of performance.
*   **Benchmarking and Peer Comparison:** Compare the portfolio's performance against a wide range of relevant market indices (e.g., S&P 500, NASDAQ Composite, Russell 2000) and peer groups. This provides context to the portfolio's returns.
*   **Performance Attribution Analysis:** Decompose the portfolio's returns to determine the primary sources of performance, such as strategic asset allocation, tactical asset allocation, and individual security selection. This helps in understanding what drives the portfolio's performance.
*   **Rolling Returns:** Analyze the portfolio's performance over various rolling time periods (e.g., 1-year, 3-year, 5-year) to understand its consistency and behavior in different market cycles.
*   **Time-Weighted vs. Money-Weighted Returns:** Differentiate between time-weighted returns (which remove the effects of cash flows) and money-weighted returns (which are affected by the timing of cash flows) to get a clearer picture of performance.

### 2. Risk Assessment and Management
This capability provides a detailed, multi-faceted analysis of the various risks associated with an investment portfolio.

*   **Volatility Measurement (Standard Deviation):** Calculate the standard deviation of the portfolio's returns to quantify its historical volatility and price fluctuations. Higher standard deviation implies higher risk.
*   **Beta and Correlation Analysis:** Measure the portfolio's sensitivity to market movements (beta) and the correlation between different assets within the portfolio. Understanding these relationships is key to diversification.
*   **Value at Risk (VaR):** Estimate the potential loss of the portfolio over a specific time horizon with a given confidence level (e.g., 95% or 99% VaR). This metric provides a single number to quantify the portfolio's risk.
*   **Conditional Value at Risk (CVaR):** Also known as Expected Shortfall, CVaR measures the expected loss of the portfolio given that the loss is greater than the VaR. It provides a more complete picture of the tail risk.
*   **Sharpe Ratio:** Calculate the risk-adjusted return of the portfolio by measuring the excess return per unit of total risk (standard deviation). A higher Sharpe ratio is generally better.
*   **Treynor Ratio:** Measure the excess return earned for each unit of systematic risk (beta). This is useful for evaluating diversified portfolios.
*   **Jensen's Alpha:** Determine the portfolio's excess return over its expected return as predicted by the Capital Asset Pricing Model (CAPM), indicating the manager's value-add.
*   **Sortino Ratio:** A modification of the Sharpe ratio that only considers downside volatility, providing a more realistic measure of risk for investors who are more concerned with losses than overall volatility.
*   **Maximum Drawdown:** Calculate the largest peak-to-trough decline in the portfolio's value, which helps in understanding the potential for large losses.
*   **Omega Ratio:** A sophisticated risk-return performance measure that considers all moments of the return distribution, providing a more comprehensive view than Sharpe or Sortino ratios.

### 3. Portfolio Optimization
This capability assists users in constructing and optimizing their portfolios to align with their specific risk tolerance and investment objectives.

*   **Modern Portfolio Theory (MPT):** Employ the principles of MPT to find the optimal asset allocation that maximizes expected return for a given level of risk. This is the foundation of modern portfolio construction.
*   **Efficient Frontier:** Generate and visualize the efficient frontier, which represents the set of optimal portfolios that offer the highest expected return for a defined level of risk. This is a powerful visualization tool.
*   **Monte Carlo Simulation:** Run thousands of simulations to project the potential future performance of the portfolio under a wide range of market conditions and economic scenarios. This helps in understanding the range of potential outcomes.
*   **Backtesting:** Test the historical performance of a given investment strategy to evaluate its viability and potential for future success.
*   **Black-Litterman Model:** A more advanced portfolio optimization model that incorporates an investor's views on the expected returns of different assets, providing a more customized and flexible approach than traditional MPT.

## Step-by-Step Workflow

1.  **Gather and Prepare Portfolio Data:**
    *   Collect historical data for each asset in the portfolio, including daily or monthly prices and dividend payments. This data is crucial for accurate analysis.
    *   The data should be in a CSV format with the following columns: `Date`, `Ticker`, `Adj Close`, `Volume`, `Dividends`.
    *   Ensure the data is clean and free of errors, such as missing values or incorrect prices.

2.  **Define the Portfolio Composition:**
    *   Create a JSON file named `portfolio.json` that clearly defines the portfolio's composition, including the tickers of the assets and their respective weights.
    *   Example `portfolio.json`:

    ```json
    {
      "name": "My Aggressive Growth Portfolio",
      "benchmark": "^GSPC",
      "risk_free_rate": 0.02,
      "assets": [
        {"ticker": "AAPL", "weight": 0.25},
        {"ticker": "GOOGL", "weight": 0.20},
        {"ticker": "MSFT", "weight": 0.20},
        {"ticker": "AMZN", "weight": 0.15},
        {"ticker": "TSLA", "weight": 0.10},
        {"ticker": "NVDA", "weight": 0.10}
      ]
    }
    ```

3.  **Execute the Analysis Script:**
    *   Use the `Read` tool to load the historical portfolio data and the `portfolio.json` file into the agent's memory.
    *   Use the `Bash` tool to execute a Python script that performs the comprehensive analysis. This script will be the core of the skill's execution.
    *   The Python script should leverage powerful libraries such as `pandas` for data manipulation, `numpy` for numerical operations, `scipy` for statistical functions, and `matplotlib` or `plotly` for data visualization.

4.  **Generate a Comprehensive Report:**
    *   The Python script should be designed to generate a detailed report in Markdown format that summarizes all the findings from the analysis.
    *   The report should be well-structured and include tables, charts, and clear explanations of all the key metrics and their implications.

5.  **Review, Interpret, and Act on the Results:**
    *   Use the `Read` tool to carefully review the generated Markdown report.
    *   Interpret the results in the context of your investment goals and risk tolerance to make well-informed decisions about your portfolio.

## Best Practices and Considerations

*   **Data Quality is Paramount:** The accuracy and reliability of the analysis are directly dependent on the quality of the input data. Always use clean, accurate, and reliable sources for historical price and dividend data.
*   **Select an Appropriate Benchmark:** The choice of benchmark is critical for meaningful performance comparison. Select a benchmark that closely mirrors the portfolio's asset allocation, investment style, and geographic focus.
*   **Acknowledge the Limitations of Historical Data:** Remember that all analysis is based on historical data and past performance is not a guarantee of future results. Use the skill's outputs as a guide for decision-making, not as a definitive prediction of the future.
*   **The Power of Diversification:** Diversification is a fundamental principle of sound risk management. A well-diversified portfolio across different asset classes, geographies, and sectors can help to reduce risk without necessarily sacrificing returns.
*   **The Importance of Regular Reviews:** Financial markets are dynamic and constantly evolving. It is essential to review your portfolio on a regular basis and make adjustments as needed to stay on track with your financial goals.
*   **Consider Transaction Costs and Taxes:** The analysis provided by this skill does not typically account for transaction costs and taxes, which can have a significant impact on real-world returns. Always consider these factors in your investment decisions.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/lkb-99) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
