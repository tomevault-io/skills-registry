---
name: transaction-categorization
description: Auto-categorizes financial transactions for QuickBooks and Booke.AI with Irish VAT rules. Trigger when user mentions transactions, receipts, bank statements, expenses, or keywords like QuickBooks, categorize, VAT. Use when this capability is needed.
metadata:
  author: bermingham85
---

# Transaction Categorization

## Instructions

### Irish VAT Rates (Apply Automatically)
- Standard: 23% - Most services/goods
- Reduced: 13.5% - Tourism, accommodation, Airbnb
- Second Reduced: 9% - Utilities, newspapers
- Zero: 0% - Exports, certain foods
- Exempt: Rent/lease
- OOS (Out of Scope): Transfers, cashback

### Known Vendors (Pre-Categorized)
- Airbnb Payments → Airbnb Income @ 13.5%
- Google Payment Ireland → Subscriptions @ 23%
- Electric Ireland → Utilities @ 9%
- PayPal → Check description, default: E-commerce expense
- Revolut Transfer → Internal Transfer (OOS)

### Account Rules
- AIB ending 1195: Personal account - EXCLUDE from business
- PayPal → Revolut Pro: Internal transfer (non-taxable)
- All Airbnb income: VAT-inclusive at 13.5%

### On Transaction Data
1. Parse file (CSV, Excel, JSON)
2. Match vendor against rules
3. If match: Apply category + VAT
4. If no match: Analyze description, suggest category
5. Flag ambiguous items for review

## n8n Webhook
POST to `http://localhost:5678/webhook/transaction/categorize`

## DO NOT
- Categorize personal AIB 1195 as business
- Guess on ambiguous items - flag for review
- Forget VAT on applicable transactions

## DO
- Auto-categorize all recognized vendors
- Apply Irish VAT rules correctly
- Generate new rules for new vendors

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/bermingham85) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
