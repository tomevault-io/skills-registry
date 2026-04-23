---
name: journal-entry-prep
description: Prepare journal entries with proper debits, credits, and supporting documentation for month-end close. Use when booking accruals, prepaid amortization, fixed asset depreciation, payroll entries, revenue recognition, or any manual journal entry. Use when this capability is needed.
metadata:
  author: fuww
---

# Journal Entry Preparation

**Important**: This skill assists with journal entry workflows but does not provide financial advice. All entries should be reviewed by qualified financial professionals before posting.

Best practices, standard entry types, documentation requirements, and review workflows for journal entry preparation.

## Standard Accrual Types and Their Entries

### Accounts Payable Accruals

Accrue for goods or services received but not yet invoiced at period end.

**Typical entry:**
- Debit: Expense account (or capitalize if asset-qualifying)
- Credit: Accrued liabilities

**Sources for calculation:**
- Open purchase orders with confirmed receipts
- Contracts with services rendered but unbilled
- Recurring vendor arrangements (utilities, subscriptions, professional services)
- Employee expense reports submitted but not yet processed

**Key considerations:**
- Reverse in the following period (auto-reversal recommended)
- Use consistent estimation methodology period over period
- Document basis for estimates (PO amount, contract terms, historical run-rate)
- Track actual vs accrual to refine future estimates

### Fixed Asset Depreciation

Book periodic depreciation expense for tangible and intangible assets.

**Typical entry:**
- Debit: Depreciation/amortization expense (by department or cost center)
- Credit: Accumulated depreciation/amortization

**Depreciation methods:**
- **Straight-line:** (Cost - Salvage) / Useful life — most common for financial reporting
- **Declining balance:** Accelerated method applying fixed rate to net book value
- **Units of production:** Based on actual usage or output vs total expected

**Key considerations:**
- Run depreciation from the fixed asset register or schedule
- Verify new additions are set up with correct useful life and method
- Check for disposals or impairments requiring write-off
- Ensure consistency between book and tax depreciation tracking

### Prepaid Expense Amortization

Amortize prepaid expenses over their benefit period.

**Typical entry:**
- Debit: Expense account (insurance, software, rent, etc.)
- Credit: Prepaid expense

**Common prepaid categories:**
- Insurance premiums (typically 12-month policies)
- Software licenses and subscriptions
- Prepaid rent (if applicable under lease terms)
- Prepaid maintenance contracts
- Conference and event deposits

**Key considerations:**
- Maintain an amortization schedule with start/end dates and monthly amounts
- Review for any prepaid items that should be fully expensed (immaterial amounts)
- Check for cancelled or terminated contracts requiring accelerated amortization
- Verify new prepaids are added to the schedule promptly

### Payroll Accruals

Accrue compensation and related costs for the period.

**Typical entries:**

*Salary accrual (for pay periods not aligned with month-end):*
- Debit: Salary expense (by department)
- Credit: Accrued payroll

*Bonus accrual:*
- Debit: Bonus expense (by department)
- Credit: Accrued bonus

*Benefits accrual:*
- Debit: Benefits expense
- Credit: Accrued benefits

*Payroll tax accrual:*
- Debit: Payroll tax expense
- Credit: Accrued payroll taxes

**Key considerations:**
- Calculate salary accrual based on working days in the period vs pay period
- Bonus accruals should reflect plan terms (target amounts, performance metrics, payout timing)
- Include employer-side taxes and benefits (FICA, FUTA, health, 401k match)
- Track PTO/vacation accrual liability if required by policy or jurisdiction

### Revenue Recognition

Recognize revenue based on performance obligations and delivery.

**Typical entries:**

*Recognize previously deferred revenue:*
- Debit: Deferred revenue
- Credit: Revenue

*Recognize revenue with new receivable:*
- Debit: Accounts receivable
- Credit: Revenue

*Defer revenue received in advance:*
- Debit: Cash / Accounts receivable
- Credit: Deferred revenue

**Key considerations:**
- Follow ASC 606 five-step framework for contracts with customers
- Identify distinct performance obligations in each contract
- Determine transaction price (including variable consideration)
- Allocate transaction price to performance obligations
- Recognize revenue as/when performance obligations are satisfied
- Maintain contract-level detail for audit support

## Supporting Documentation Requirements

Every journal entry should have:

1. **Entry description/memo:** Clear, specific description of what the entry records and why
2. **Calculation support:** How amounts were derived (formula, schedule, source data reference)
3. **Source documents:** Reference to the underlying transactions or events (PO numbers, invoice numbers, contract references, payroll register)
4. **Period:** The accounting period the entry applies to
5. **Preparer identification:** Who prepared the entry and when
6. **Approval:** Evidence of review and approval per the authorization matrix
7. **Reversal indicator:** Whether the entry auto-reverses and the reversal date

## Review and Approval Workflows

### Typical Approval Matrix

| Entry Type | Amount Threshold | Approver |
|-----------|-----------------|----------|
| Standard recurring | Any amount | Accounting manager |
| Non-recurring / manual | < $50K | Accounting manager |
| Non-recurring / manual | $50K - $250K | Controller |
| Non-recurring / manual | > $250K | CFO / VP Finance |
| Top-side / consolidation | Any amount | Controller or above |
| Out-of-period adjustments | Any amount | Controller or above |

*Note: Thresholds should be set based on your organization's materiality and risk tolerance.*

### Review Checklist

Before approving a journal entry, the reviewer should verify:

- [ ] Debits equal credits (entry is balanced)
- [ ] Correct accounting period (not posting to a closed period)
- [ ] Account codes exist and are appropriate for the transaction
- [ ] Amounts are mathematically accurate and supported by calculations
- [ ] Description is clear, specific, and sufficient for audit purposes
- [ ] Department/cost center/project coding is correct
- [ ] Treatment is consistent with prior periods and accounting policies
- [ ] Auto-reversal is set appropriately (accruals should reverse)
- [ ] Supporting documentation is complete and referenced
- [ ] Entry amount is within the preparer's authority level
- [ ] No duplicate of an existing entry
- [ ] Unusual or large amounts are explained and justified

## Common Errors to Check For

1. **Unbalanced entries:** Debits do not equal credits (system should prevent, but check manual entries)
2. **Wrong period:** Entry posted to an incorrect or already-closed period
3. **Wrong sign:** Debit entered as credit or vice versa
4. **Duplicate entries:** Same transaction recorded twice (check for duplicates before posting)
5. **Wrong account:** Entry posted to incorrect GL account (especially similar account codes)
6. **Missing reversal:** Accrual entry not set to auto-reverse, causing double-counting
7. **Stale accruals:** Recurring accruals not updated for changed circumstances
8. **Round-number estimates:** Suspiciously round amounts that may not reflect actual calculations
9. **Incorrect FX rates:** Foreign currency entries using wrong exchange rate or date
10. **Missing intercompany elimination:** Entries between entities without corresponding elimination
11. **Capitalization errors:** Expenses that should be capitalized, or capitalized items that should be expensed
12. **Cut-off errors:** Transactions recorded in the wrong period based on delivery or service date

## FashionUnited Journal Entry Types

FashionUnited uses Google Sheets for journal entry preparation with entries recorded in the trial balance workbook.

### FashionUnited Revenue Recognition Entries

**Display Advertising Revenue:**
- Debit: 1100 Accounts Receivable
- Credit: 4100 Advertising Revenue
- Timing: Recognize over impression delivery period
- Support: Ad server delivery report matched to Vtiger invoice

**Sponsored Content Revenue:**
- Debit: 1100 Accounts Receivable
- Credit: 4100 Advertising Revenue
- Timing: Recognize on publication date
- Support: CMS publication confirmation, Vtiger invoice

**Job Posting Revenue:**
- Debit: 1100 Accounts Receivable
- Credit: 4200 Job Posting Revenue (portion earned)
- Credit: 2300 Deferred Revenue (unearned portion)
- Timing: Recognize over posting duration (typically 30 days)
- Support: Job board posting dates, Vtiger invoice
- Calculation: Invoice amount × (days posted in period / total posting days)

**Employer Branding Revenue:**
- Debit: 1100 Accounts Receivable (or 2300 Deferred Revenue if prepaid)
- Credit: 4300 Employer Branding Revenue
- Timing: Recognize ratably over contract term
- Support: Vtiger contract, billing schedule
- Calculation: Annual contract value / 12 months

**Subscription Revenue:**
- Debit: 2300 Deferred Revenue
- Credit: 4400 Subscription Revenue
- Timing: Recognize ratably over subscription period
- Support: Vtiger subscription record, payment date
- Calculation: Monthly subscription value for period

**Media Partnership Revenue:**
- Debit: 1100 Accounts Receivable
- Credit: 4500 Partnership Revenue
- Timing: Per deliverable or ratably over term
- Support: Partnership contract, deliverable confirmation

### FashionUnited FX Revaluation Entry

At month-end, revalue all foreign currency monetary items:

- Debit/Credit: 1000 Cash (per currency account)
- Debit/Credit: 1100 Accounts Receivable (foreign currency)
- Debit/Credit: 2000 Accounts Payable (foreign currency)
- Offset: 7100 FX Gains/Losses

**Process:**
1. Export all foreign currency balances from Google Sheets TB
2. Apply closing FX rate (ECB rates for EUR cross-rates)
3. Calculate difference from book rate
4. Post single entry for net FX gain/loss

### FashionUnited VAT Entries

**VAT on Sales (within EU):**
- Debit: 1100 Accounts Receivable (gross)
- Credit: 4XXX Revenue (net)
- Credit: 2200 VAT Payable (VAT amount)

**Reverse Charge (B2B cross-border services):**
- Debit: 1500 VAT Receivable
- Credit: 2200 VAT Payable
- (Self-assessed VAT nets to zero)

**VAT Payment:**
- Debit: 2200 VAT Payable
- Credit: 1000 Cash

### FashionUnited Common Accruals

**Hosting/Infrastructure Accrual:**
- Debit: 5100 Hosting Costs
- Credit: 2100 Accrued Expenses
- Frequency: Monthly (reverse in following period)
- Basis: Estimated usage from cloud provider dashboards

**Freelance Content Accrual:**
- Debit: 5200 Content Costs
- Credit: 2100 Accrued Expenses
- Frequency: Monthly
- Basis: Articles delivered but not yet invoiced

**Partner Revenue Share Accrual:**
- Debit: 5300 Partner Revenue Share
- Credit: 2100 Accrued Expenses
- Frequency: Monthly/Quarterly per contract
- Basis: Calculated share of partnership revenue

### FashionUnited Approval Matrix

| Entry Type | Amount Threshold | Approver |
|-----------|-----------------|----------|
| Standard recurring (depreciation, amortization) | Any | Finance Manager self-review |
| Revenue recognition | Any | Finance Manager |
| Accruals | < EUR 5,000 | Finance Manager |
| Accruals | > EUR 5,000 | Finance Manager + Leadership review |
| FX revaluation | Any | Finance Manager |
| Manual adjustments | < EUR 1,000 | Finance Manager |
| Manual adjustments | > EUR 1,000 | Finance Manager + Leadership review |
| Out-of-period adjustments | Any | Finance Manager + Leadership review |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/fuww) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
