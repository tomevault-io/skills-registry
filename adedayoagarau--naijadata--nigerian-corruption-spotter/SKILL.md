---
name: nigerian-corruption-spotter
description: | Use when this capability is needed.
metadata:
  author: adedayoagarau
---

# Nigerian Corruption Spotter

## Quick Start Workflow
1. LOAD budget data (PDF, JSON, or structured text)
2. RUN red flag scan (see references/red-flags.md)
3. CALCULATE anomaly scores (see references/benchmarks.md)
4. QUANTIFY impact (see references/impact-calculator.md)
5. GENERATE report with findings

## Red Flag Categories

### Category A: Structural (High Confidence)
- Budget padding: Line items 200%+ above previous year
- Phantom projects: Capital allocations with no implementation
- Vague descriptions: "Miscellaneous", "Sundry" over ₦100M

### Category B: Ratio Anomalies
- Overhead > Capital in development MDAs
- Travel > Service Delivery costs
- Admin > 30% of total allocation

### Category C: Comparative
- Allocation 150%+ above peer states
- 100%+ YoY increase without policy change

## Risk Scoring
Score = (Structural Flags × 3) + (Ratio Anomalies × 2) + (Comparative × 1)
- 0-3: Low | 4-6: Medium | 7-9: High | 10+: Critical

## Key References
- references/red-flags.md - Detection rules
- references/benchmarks.md - Nigerian cost standards
- references/impact-calculator.md - Naira to impact conversion
- references/corruption-history.md - Historical cases
- scripts/impact_calculator.py - Calculation tool
- scripts/red_flag_scanner.py - Automated scanning

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/adedayoagarau) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
