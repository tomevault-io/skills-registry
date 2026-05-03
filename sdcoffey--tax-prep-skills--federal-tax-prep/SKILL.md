---
name: federal-tax-prep
description: Prepare and review simple U.S. individual federal income tax returns for tax year 2025 using Form 1040/1040-SR and common schedules. Use when Codex needs to determine filing requirements, choose filing status, compare standard vs. itemized deduction, apply core credits (Child Tax Credit, EITC, Child and Dependent Care Credit), and generate a clear filing checklist from taxpayer documents (W-2, 1099 series, basic deduction/credit inputs). Federal filings only. Use when this capability is needed.
metadata:
  author: sdcoffey
---

# Simple Federal Tax Prep

## Overview

Use this skill to run a conservative, source-cited workflow for straightforward 2025 federal 1040 filings.

## Resource Loading

Load `references/tax-year-2025-federal.md` at the start of every run.
Load `references/irs-guides.md` when you need authoritative instructions, worksheets, or schedule-specific rules.
Use `assets/forms/f1040-fillable.pdf` when the user provides or requests filling the 1040 form.

## Scope Guardrails

Keep work limited to federal individual returns and common schedules.
Flag and pause for confirmation before handling complex topics: self-employment expenses/depreciation, rental activity, multi-state issues, foreign reporting, trusts/estates, large capital transactions, or unclear legal interpretations.
State that calculations are informational and recommend a licensed tax professional for legal advice.

## Workflow

1. Confirm tax year and filing scope.
Use tax year 2025 unless the user explicitly asks for another year.
Confirm the return is federal only and document any assumptions.

2. Collect taxpayer facts and source documents.
Gather filing status, ages, dependent details, W-2/1099 data, withholding, estimated payments, and major deduction/credit inputs.
List missing items before calculating.

3. Determine filing requirement and filing status.
Use the filing threshold table in `references/tax-year-2025-federal.md`.
If filing is optional but potentially beneficial (refund/credits), note that clearly.

4. Choose deduction path.
Compute standard deduction first.
Check itemized deductions only when user provides likely qualifying amounts (medical, taxes, interest, charity, casualty where allowed).
Use Schedule A limits from `references/tax-year-2025-federal.md`.

5. Build adjusted gross income and taxable income.
Map income and adjustments to Form 1040/Schedule 1 lines.
Show each subtotal to make review easy.

6. Compute tax and apply credits.
Use Form 1040 instructions/tables for tax.
Evaluate core credits in this order: Child Tax Credit/ODC, Child and Dependent Care Credit, EITC.
Cite worksheet/form used for each credit.

7. Reconcile payments and determine balance/refund.
Apply withholding, estimated payments, and refundable credits.
Report amount owed or expected refund.

8. Prepare filing checklist.
List required forms/schedules, signature requirements, e-file/mail path, deadline, and extension options.
Call out any follow-up documents still needed.

9. Provide audit-friendly output.
Include assumptions, computed values, and citations to IRS sources.
Highlight uncertainty areas and next actions.

## Output Format

Return results in this order:
- `Return summary`: filing status, dependents, AGI, taxable income, tax, credits, payments, refund/balance due.
- `Forms and schedules`: exact federal forms needed.
- `Assumptions and missing info`: unresolved facts.
- `Citations`: IRS links used for each major rule/value.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sdcoffey) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
