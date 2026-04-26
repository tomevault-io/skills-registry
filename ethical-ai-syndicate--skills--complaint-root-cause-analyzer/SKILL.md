---
name: complaint-root-cause-analyzer
description: Use when analyzing customer complaints to identify root causes from 50 predefined categories. Use after receiving complaint data. Produces structured classification with reasoning, sentiment, urgency, and resolution suggestions.
metadata:
  author: ethical-ai-syndicate
---

# Complaint Root Cause Analyzer

## Overview

AI-powered system for analyzing customer complaints in financial services, identifying root causes from 50 predefined categories across 7 domains, and suggesting appropriate resolutions.

**Core principle:** Classification happens in seconds with consistent category definitions, actionable resolution templates, and traceable reasoning.

## When to Use

- Processing customer complaint data for root cause analysis
- Building complaint classification systems
- Demonstrating AI complaint categorization to stakeholders
- Prototyping complaint handling workflows

## Output Format

```yaml
classification:
  primary_root_cause: "[Category Code] - [Category Name]"
  domain: "[Domain Name]"
  confidence: "[HIGH | MEDIUM | LOW]"
  reasoning: "[Explanation of classification]"

secondary_categories: ["[Additional relevant categories]"]

sentiment:
  level: "[angry | frustrated | concerned | neutral]"
  urgency: "[critical | high | medium | low]"

entities:
  amounts: ["[Extracted financial amounts]"]
  dates: ["[Relevant dates]"]
  products: ["[Products mentioned]"]

resolution:
  suggested_action: "[Resolution based on category template]"
  escalation_required: [true | false]
```

## Root Cause Categories (50 Total)

| Domain | Categories | Examples |
|--------|------------|----------|
| Account Management | 8 | Account opening delays, closure issues, blocked access |
| Fees and Charges | 8 | Unexpected fees, overdraft charges, wire transfer fees |
| Transactions | 8 | Failed transactions, duplicates, unauthorized charges |
| Cards | 8 | Card not received, blocked cards, fraud, rewards issues |
| Loans | 6 | Application rejection, rate discrepancies, payment issues |
| Customer Service | 6 | Wait times, unhelpful staff, unresolved complaints |
| Digital Banking | 6 | App crashes, login issues, 2FA problems |

## Usage

```bash
# Run full demo
python demo.py

# Run specific complaint
python demo.py --complaint 1

# List all categories
python demo.py --list-categories

# Interactive mode
python demo.py --interactive

# JSON output
python demo.py --json 1
```

## Supporting Files

- `demo.py` - Main demo script
- `analyzer.py` - Core analysis logic
- `root_cause_categories.py` - 50 categories + resolution templates
- `sample_complaints.py` - 8 sample complaints with expected outputs
- `LIMITATIONS.md` - What the prototype does not cover

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ethical-ai-syndicate) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
