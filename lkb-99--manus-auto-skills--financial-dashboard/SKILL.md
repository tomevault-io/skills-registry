---
name: financial-dashboard
description: Create personal and business financial dashboards with interactive visualizations. Use this skill when users want to create, manage, or analyze financial dashboards, track financial health, or visualize financial data. Triggers: financial dashboard, personal finance, business finance, KPI tracking, data visualization, dashboard financeiro, finanças pessoais, finanças empresariais, acompanhamento de KPIs, visualização de dados. Use when this capability is needed.
metadata:
  author: lkb-99
---

# Financial Dashboard

## Overview
This skill empowers Manus to create, manage, and analyze personal and business financial dashboards. It leverages data processing and visualization capabilities to provide interactive and insightful dashboards. By connecting to various data sources, this skill can generate a comprehensive overview of financial health, track key performance indicators (KPIs), and help in making data-driven decisions. Whether for personal budgeting or for complex corporate financial analysis, this skill provides the tools to build a customized and effective financial dashboard.

## Automatic Triggers

**ALWAYS activate this skill when user mentions:**
- Keywords: financial dashboard, personal finance, business finance, KPI tracking, data visualization, dashboard financeiro, finanças pessoais, finanças empresariais, acompanhamento de KPIs, visualização de dados, financial health, saúde financeira, investment portfolio, portfólio de investimentos.
- Phrases: "create a financial dashboard", "track my expenses", "visualize my revenue", "monitor my investments", "criar um dashboard financeiro", "acompanhar meus gastos", "visualizar minha receita", "monitorar meus investimentos".
- Context: Any discussion about creating, managing, or analyzing financial data through a dashboard interface.

**Example user queries that trigger this skill:**
- "I want to create a dashboard to track my personal finances."
- "Can you help me build a financial dashboard for my business?"
- "I need to visualize my company's financial performance."
- "Quero criar um painel para acompanhar minhas finanças pessoais."
- "Você pode me ajudar a construir um dashboard financeiro para minha empresa?"
- "Preciso visualizar o desempenho financeiro da minha empresa."


## When to Use This Skill
This skill is particularly useful in the following scenarios:

*   **Personal Finance Management:** When you need to track your income, expenses, savings, and investments to get a clear picture of your financial situation.
*   **Business Performance Tracking:** For business owners and managers who need to monitor financial KPIs, such as revenue, profitability, and cash flow, in real-time.
*   **Financial Reporting:** When you need to create automated and interactive financial reports for stakeholders, investors, or internal teams.
*   **Investment Portfolio Analysis:** For investors who want to monitor the performance of their investment portfolios and make informed decisions.
*   **Budgeting and Forecasting:** When you need to create budgets, set financial goals, and forecast future financial performance.

## Core Capabilities

### 1. Data Integration and Processing
This skill can connect to various data sources to gather financial data, including:

*   **CSV and Excel Files:** Import financial data from spreadsheets.
*   **Databases:** Connect to SQL databases to retrieve financial records.
*   **APIs:** Integrate with financial APIs to get real-time data (e.g., stock prices, bank transactions).

The skill can process and clean the imported data to ensure accuracy and consistency. This includes handling missing values, converting data types, and structuring the data for analysis.

### 2. Key Performance Indicator (KPI) Tracking
The skill allows you to define and track a wide range of financial KPIs. These can be customized based on the specific needs of the user.

**Personal Finance KPIs:**
*   **Net Worth:** Total Assets - Total Liabilities
*   **Savings Rate:** (Income - Expenses) / Income
*   **Debt-to-Income Ratio:** Total Monthly Debt Payments / Gross Monthly Income
*   **Investment Portfolio Return:** The gain or loss on an investment portfolio over a specific period.

**Business Finance KPIs:**
*   **Gross Profit Margin:** (Net Sales – Cost of Goods Sold) / Net Sales
*   **Net Profit Margin:** Net Income / Revenue
*   **Operating Cash Flow:** Cash generated from normal business operations.
*   **Current Ratio:** Current Assets / Current Liabilities
*   **Customer Acquisition Cost (CAC):** The cost of acquiring a new customer.
*   **Customer Lifetime Value (LTV):** The total revenue a business can expect from a single customer.

### 3. Interactive Visualizations
The skill can generate a variety of interactive charts and graphs to visualize financial data. This makes it easier to understand trends, patterns, and outliers.

**Supported Chart Types:**
*   **Line Charts:** For tracking trends over time (e.g., revenue growth, stock prices).
*   **Bar Charts:** For comparing financial metrics across different categories (e.g., expenses by category).
*   **Pie Charts:** For showing the composition of a whole (e.g., portfolio allocation).
*   **Tables:** For displaying detailed financial data in a structured format.
*   **Gauges and Scorecards:** For displaying single-value KPIs and their performance against a target.

### 4. Customizable Dashboards
The skill allows you to create fully customizable dashboards. You can choose which KPIs and visualizations to include, and arrange them in a way that makes sense for your needs. You can also set up alerts and notifications for specific financial events.

## Step-by-Step Workflow

1.  **Initiate the Skill:** Start by invoking the `financial-dashboard` skill.
2.  **Define the Dashboard Scope:** Specify whether you want to create a personal or business financial dashboard.
3.  **Connect Data Sources:** Provide the necessary information to connect to your financial data sources (e.g., upload a CSV file, provide database credentials, or API keys).
4.  **Select KPIs:** Choose the KPIs you want to track from a predefined list or define your own custom KPIs.
5.  **Configure Visualizations:** Select the chart types and visualizations you want to include in your dashboard.
6.  **Generate the Dashboard:** Manus will process the data, calculate the KPIs, and generate the interactive dashboard.
7.  **Analyze and Interact:** Interact with the dashboard by filtering data, drilling down into details, and exploring different visualizations.
8.  **Export and Share:** Export the dashboard as a PDF or share a link to the interactive dashboard with others.

## Best Practices

*   **Start with Clear Goals:** Before creating a dashboard, define what you want to achieve with it. What questions do you want to answer? What decisions do you want to make?
*   **Focus on the Right Metrics:** Don't try to track everything. Focus on the KPIs that are most relevant to your goals.
*   **Keep it Simple and Clean:** A good dashboard should be easy to read and understand. Avoid clutter and unnecessary information.
*   **Use Visualizations Effectively:** Choose the right chart type for the data you are presenting. Use colors and labels to make your visualizations clear and informative.
*   **Provide Context:** Don't just show the numbers. Provide context and explanations to help users understand what the data means.
*   **Ensure Data Accuracy:** The insights from your dashboard are only as good as the data it is based on. Make sure your data is accurate, complete, and up-to-date.
*   **Iterate and Improve:** A dashboard is not a one-time project. Continuously gather feedback from users and make improvements to your dashboard over time.

## Examples

### Example 1: Personal Finance Dashboard

**Goal:** To get a comprehensive overview of my personal finances and track my progress towards my savings goals.

**Data Sources:**
*   CSV file with bank account transactions.
*   CSV file with credit card statements.
*   API connection to a brokerage account for investment data.

**KPIs:**
*   Net Worth
*   Monthly Income vs. Expenses
*   Savings Rate
*   Top 5 Expense Categories
*   Investment Portfolio Performance

**Visualizations:**
*   A scorecard showing the current Net Worth.
*   A bar chart comparing monthly income and expenses.
*   A gauge showing the current Savings Rate against a target of 20%.
*   A pie chart showing the breakdown of expenses by category.
*   A line chart showing the performance of the investment portfolio over time.

### Example 2: Business Financial Dashboard for a SaaS Company

**Goal:** To monitor the financial health of the company and track key growth metrics.

**Data Sources:**
*   Connection to a Stripe account for subscription data.
*   Connection to a QuickBooks account for accounting data.
*   Connection to a Google Analytics account for website traffic data.

**KPIs:**
*   Monthly Recurring Revenue (MRR)
*   Customer Churn Rate
*   Customer Lifetime Value (LTV)
*   Customer Acquisition Cost (CAC)
*   LTV to CAC Ratio
*   Cash Runway

**Visualizations:**
*   A line chart showing the MRR growth over time.
*   A bar chart showing the number of new customers vs. churned customers each month.
*   A scorecard showing the current LTV to CAC Ratio.
*   A table showing the cash runway in months.

### Example Code Snippet (Python with Pandas and Matplotlib)

```python
import pandas as pd
import matplotlib.pyplot as plt

# Load financial data from a CSV file
data = pd.read_csv('financial_data.csv')

# Calculate total revenue
total_revenue = data['revenue'].sum()

# Calculate total expenses
total_expenses = data['expenses'].sum()

# Calculate net income
net_income = total_revenue - total_expenses

# Create a bar chart to visualize revenue and expenses
labels = ['Revenue', 'Expenses', 'Net Income']
values = [total_revenue, total_expenses, net_income]

plt.figure(figsize=(10, 6))
plt.bar(labels, values, color=['green', 'red', 'blue'])
plt.title('Financial Summary')
plt.ylabel('Amount (USD)')
plt.show()
```

## References

*   [How to Build a Financial Dashboard: A Step-by-Step Guide](https://www.clearpointstrategy.com/blog/financial-dashboard)
*   [Financial Dashboard: 24 Metrics You Should Include](https://finmark.com/financial-dashboard/)
*   [12 Financial Dashboard Examples & Templates](https://www.qlik.com/us/dashboard-examples/financial-dashboards)
*   [21 financial KPIs and metrics you should track](https://www.thoughtspot.com/data-trends/dashboard/financial-kpis-and-metrics-dashboard-examples)
*   [30 Financial Metrics and KPIs to Measure Success](https://www.netsuite.com/portal/resource/articles/accounting/financial-kpis-metrics.shtml)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/lkb-99) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
