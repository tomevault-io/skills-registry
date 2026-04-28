---
name: financial-analysis
description: Comprehensive financial analysis toolkit combining ratio analysis (ROE, ROA, P/E, liquidity, profitability) with advanced modeling (DCF, Monte Carlo, sensitivity testing, scenario planning) for investment decisions and company valuation. Use when this capability is needed.
metadata:
  author: seqis
---

# Financial Analysis Skill

Complete financial analysis and modeling toolkit for investment decisions, company valuation, and risk assessment.

---

## Part 1: Financial Ratio Analysis

### Capabilities
- **Profitability**: ROE, ROA, Gross Margin, Operating Margin, Net Margin
- **Liquidity**: Current Ratio, Quick Ratio, Cash Ratio
- **Leverage**: Debt-to-Equity, Interest Coverage, Debt Service Coverage
- **Efficiency**: Asset Turnover, Inventory Turnover, Receivables Turnover
- **Valuation**: P/E, P/B, P/S, EV/EBITDA, PEG
- **Per-Share**: EPS, Book Value per Share, Dividend per Share

### Key Ratio Formulas

| Ratio | Formula | Good Range |
|-------|---------|------------|
| ROE | Net Income / Shareholders' Equity | >15% |
| ROA | Net Income / Total Assets | >5% |
| Current Ratio | Current Assets / Current Liabilities | 1.5-3.0 |
| Quick Ratio | (Current Assets - Inventory) / Current Liabilities | >1.0 |
| Debt-to-Equity | Total Debt / Shareholders' Equity | <2.0 |
| P/E | Price / EPS | Industry-dependent |
| EV/EBITDA | Enterprise Value / EBITDA | 8-12x typical |

### Input Formats
- CSV/Excel with financial line items
- JSON with structured financial statements
- Text description of key figures

---

## Part 2: Financial Modeling

### Core Capabilities

#### 1. Discounted Cash Flow (DCF)
- Multi-scenario growth projections
- Terminal value (perpetuity growth + exit multiple)
- WACC calculation
- Enterprise and equity valuations

#### 2. Sensitivity Analysis
- Key assumption impact testing
- Tornado charts for driver ranking
- Break-even analysis

#### 3. Monte Carlo Simulation
- 1,000-10,000 scenario iterations
- Probability distributions for inputs
- Confidence intervals (90%, 95%)
- VaR and risk metrics

#### 4. Scenario Planning
- Best/Base/Worst cases
- Probability-weighted expected values
- Decision tree analysis

### DCF Input Requirements
- Historical financials (3-5 years)
- Revenue growth assumptions
- Operating margin projections
- CapEx forecasts
- Working capital requirements
- Terminal growth rate / exit multiple
- Discount rate (risk-free rate, beta, market premium)

### Model Types Supported
1. **Corporate Valuation** - Mature, growth, turnaround
2. **Project Finance** - Infrastructure, real estate, energy
3. **M&A Analysis** - Acquisition valuation, synergy modeling
4. **LBO Models** - Leveraged buyout, IRR/MOIC analysis

---

## Output Formats

### Ratio Analysis Output
- Calculated ratios with values
- Industry benchmark comparisons
- Trend analysis (multi-period)
- Interpretation and flags

### DCF Model Output
- Financial projections
- Free cash flow calculations
- Terminal value computation
- Valuation summary
- Excel workbook

### Monte Carlo Output
- Probability distribution
- Confidence intervals
- Statistical summary (mean, median, std dev)
- Risk metrics (VaR)

---

## Best Practices

### Modeling Standards
- Consistent formatting and structure
- Clear assumption documentation
- Separation of inputs/calculations/outputs
- Error checking and validation

### Valuation Principles
- Use multiple methods for triangulation
- Apply appropriate risk adjustments
- Validate against trading multiples
- Document key assumptions

### Quality Checks
1. Balance sheet balancing
2. Cash flow reconciliation
3. Circular reference resolution
4. Sensitivity bound checking
5. Statistical validation

---

## Example Usage

**Ratio Analysis:**
- "Calculate key financial ratios for this company"
- "What's the P/E ratio if price is $50 and EPS is $2.50?"
- "Analyze liquidity using the balance sheet"

**Modeling:**
- "Build a DCF model using the attached financials"
- "Run Monte Carlo with 5,000 iterations"
- "Create sensitivity analysis for growth rate and WACC"
- "Develop three scenarios with probability weights"

---

## Limitations

- Models are only as good as assumptions
- Past performance ≠ future results
- Industry benchmarks are general guidelines
- Not a substitute for professional financial advice
- Professional judgment required for interpretation

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/seqis) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
