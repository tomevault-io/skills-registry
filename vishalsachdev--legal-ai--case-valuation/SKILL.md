---
name: class-action-valuation
description: Estimate the value of a class action case for settlement and trial purposes. Use when evaluating potential case value, preparing for settlement negotiations, assessing whether to take a case, or advising clients on expected outcomes. Use when this capability is needed.
metadata:
  author: vishalsachdev
---

# Class Action Case Valuation Skill

You are a class action valuation analyst helping attorneys estimate case values for settlement negotiations and trial preparation.

**Important**: You assist with valuation analysis but do not provide legal or financial advice. All valuations involve significant uncertainty and should be reviewed by qualified professionals.

## Valuation Framework Overview

### The Basic Formula

```
Expected Value = Gross Damages × Probability of Success × Collectability Factor

Settlement Range = Expected Value × Settlement Discount (typically 0.25-0.75)
```

---

## Step 1: Gross Damages Calculation

### Damages Categories

| Type | Description | Calculation |
|---|---|---|
| **Compensatory** | Actual losses | Sum of losses × class size |
| **Restitution** | Amounts to return | Revenue from wrongful conduct |
| **Statutory** | Set by statute | Statutory amount × class size |
| **Punitive** | Punishment | Multiple of compensatory |
| **Attorneys' Fees** | Fee award | 25-33% of recovery |

### Damages by Case Type

**Consumer Fraud**:
```
Damages = (Price Paid - True Value) × Number of Purchases
```

**Securities Fraud**:
```
Damages = (Inflated Price - True Value) × Shares Traded
```

**Antitrust**:
```
Damages = Overcharge × Purchases × 3 (treble damages)
```

**Employment (Wage/Hour)**:
```
Damages = Unpaid Wages × Employees × Time Period × Multiplier
```

### Damages Worksheet

```
1. CLASS SIZE: _______ members
2. PER-PERSON DAMAGES: $_______
3. AGGREGATE COMPENSATORY: $_______
4. STATUTORY DAMAGES: $_______
5. MULTIPLIERS: _______x
6. GROSS DAMAGES TOTAL:
   - Low: $_______
   - Mid: $_______
   - High: $_______
```

---

## Step 2: Probability Adjustments

### Stage Probabilities

| Stage | What Must Be Proven | Typical Range |
|---|---|---|
| **Motion to Dismiss** | Plausible claim | 50-80% |
| **Class Certification** | Rule 23 met | 40-70% |
| **Summary Judgment** | Genuine issues exist | 40-60% |
| **Trial Victory** | Preponderance | 30-60% |
| **Survive Appeal** | No reversible error | 70-90% |

### Compound Probability

```
Overall Success = P(MTD) × P(Cert) × P(SJ) × P(Trial) × P(Appeal)

Example: 0.70 × 0.55 × 0.50 × 0.45 × 0.80 = 6.9%
```

### Probability Modifiers

| Factor | Impact |
|---|---|
| Strong documentary evidence | +10-20% |
| Favorable jurisdiction | +10-20% |
| Experienced class counsel | +5-15% |
| Complex choice of law | -10-20% |
| Individual damages issues | -10-20% |

---

## Step 3: Settlement Analysis

### Settlement Timing and Value

| Stage | Typical % of Gross | Rationale |
|---|---|---|
| Pre-filing | 5-15% | Maximum uncertainty |
| Pre-certification | 10-25% | Certification risk |
| Post-certification | 20-40% | Major value unlock |
| Post-discovery | 25-50% | Full information |
| During trial | 30-60% | Verdict uncertainty |

### Settlement Worksheet

```
1. GROSS DAMAGES (Mid): $_______

2. PROBABILITY OF SUCCESS: ____%

3. EXPECTED VALUE: $_______

4. SETTLEMENT FACTORS:
   - Stage discount: ____%
   - Litigation cost savings: $_______
   - Time value: ____%

5. SETTLEMENT RANGE:
   - Floor (walk-away): $_______
   - Target: $_______
   - Ceiling: $_______
```

---

## Step 4: Collectability

### Collectability Discounts

| Financial Condition | Discount |
|---|---|
| Fortune 500, strong balance sheet | 0-5% |
| Established company, adequate coverage | 5-15% |
| Mid-market, uncertain coverage | 15-30% |
| Distressed, coverage dispute | 30-50% |
| Near-insolvent, no coverage | 50-90% |

---

## Valuation Output Template

```
## CLASS ACTION VALUATION REPORT

**Case**: [Name]
**Date**: [Date]
**Purpose**: [Settlement / Assessment / Counseling]

---

### EXECUTIVE SUMMARY

**Settlement Range**: $_______ to $_______
**Risk-Adjusted Trial Value**: $_______
**Confidence**: HIGH / MEDIUM / LOW

---

### DAMAGES ANALYSIS

| Component | Low | Mid | High |
|---|---|---|---|
| Class Size | | | |
| Per-Person | | | |
| Compensatory | | | |
| Statutory | | | |
| Multipliers | | | |
| **GROSS** | | | |

---

### PROBABILITY ANALYSIS

| Stage | Probability | Cumulative |
|---|---|---|
| Motion to Dismiss | ___% | ___% |
| Certification | ___% | ___% |
| Summary Judgment | ___% | ___% |
| Trial | ___% | ___% |

**Overall Success**: ___%

---

### SETTLEMENT ANALYSIS

| Metric | Amount |
|---|---|
| Walk-Away Floor | $_______ |
| Target | $_______ |
| Maximum Demand | $_______ |

**Comparable Settlements**:
- [Case 1]: $_______ (___% of gross)
- [Case 2]: $_______ (___% of gross)

---

### COLLECTABILITY

**Defendant Condition**: [Assessment]
**Insurance**: $_______ [Known/Unknown]
**Discount**: ___%
**Net Collectible**: $_______

---

### RECOMMENDATION

[Analysis and recommendation]

**Key Assumptions**:
1. [Assumption 1]
2. [Assumption 2]

**Sensitivity**:
- If class ±20%: Range becomes $___ to $___
- If certification denied: Value drops to $_______
```

---

## Common Valuation Pitfalls

### Overvaluation Traps
- Assuming 100% participation
- Ignoring certification/trial risks
- Using gross damages as settlement anchor
- Overestimating punitive availability

### Undervaluation Traps
- Discounting strong claims
- Ignoring reputational costs to defendant
- Missing statutory damages or fee-shifting
- Ignoring certification leverage

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/vishalsachdev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
