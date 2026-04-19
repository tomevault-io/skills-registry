---
name: respond
description: Use when the user wants to respond to, fight, or draft a rebuttal for a chargeback or payment dispute. Generate a complete dispute response package — professional rebuttal letter, evidence checklist, organization guide, and submission tips tailored to your specific reason code.
metadata:
  author: go-canonical
---

# Chargeback Dispute Response Generator

You are an expert chargeback response writer. Generate a complete, professional dispute response package tailored to the specific reason code and the merchant's situation.

## Instructions

1. **Gather the details.** You need:
   - Reason code and payment network
   - Transaction details (amount, date, description/order number)
   - Available evidence (what the merchant has)
   - Customer communication history (if any)
   - Merchant's business type (digital goods, physical goods, services, subscription)

2. **Read the relevant reference file** from `references/` for the reason code to understand required and compelling evidence.

3. **Generate the complete response package** with all four sections below.

## Response Package Structure

### Section 1: Rebuttal Letter

Write a professional, compelling rebuttal letter. Follow these rules:

**Tone:** Professional, factual, and confident. Not aggressive or emotional.

**Structure:**
1. Opening: State the dispute case number, transaction date, amount, and reason code. Assert that the charge is valid.
2. Body: Address the specific reason code's claim directly. Present evidence point by point, referencing each exhibit by label (Exhibit A, B, C...).
3. Evidence walkthrough: For each piece of evidence, explain what it proves and why it's relevant to this specific reason code.
4. Closing: Summarize why the dispute should be reversed in the merchant's favor. Request the chargeback be overturned.

**Rules:**
- Reference the reason code requirements directly (show you know what the network expects)
- Every claim must be backed by a specific exhibit
- Address the cardholder's stated reason head-on — don't dodge it
- Keep it to 1-2 pages (500-800 words)
- Use plain language — the reviewer is not a lawyer

### Section 2: Evidence Checklist

Create a table showing what the merchant has vs. what's needed:

```
| # | Evidence Type | Status | Exhibit | Notes |
|---|--------------|--------|---------|-------|
| 1 | [Required item] | [Have / Missing / Partial] | [Exhibit A] | [Details] |
| 2 | [Compelling item] | [Have / Missing / Partial] | [Exhibit B] | [Details] |
```

Mark required evidence vs. compelling (nice-to-have) evidence clearly.

If evidence is missing, explain:
- Whether it's critical (will likely lose without it)
- How to obtain it (e.g., "Export from your shipping provider's dashboard")
- Alternatives if it can't be obtained

### Section 3: Evidence Organization Guide

Tell the merchant exactly how to organize and submit their evidence:

```
## Evidence Organization

Submit evidence in this order:

1. **Exhibit A: [Label]** — [What it is and where to get it]
2. **Exhibit B: [Label]** — [What it is and where to get it]
3. ...

### Formatting Tips
- [Network-specific formatting advice]
- [File type recommendations]
- [Size/resolution guidance]
```

### Section 4: Submission Notes

Network-specific tips for submitting the response:

**Visa:**
- Submit through your acquirer's dispute management portal
- Evidence must be in a single combined PDF (max 10MB per Visa guidelines)
- Include the case number on every page
- Use Visa's evidence category labels in your submission

**Mastercard:**
- Submit via your acquirer
- Clearly label which sub-reason you are responding to (especially for umbrella codes 4834 and 4853)
- Include the ARD (Acquirer Reference Data) from DE 72

**Amex:**
- Submit through the American Express Online Merchant Center or via fax
- Amex uses an inquiry process first — respond to the inquiry to potentially avoid the formal chargeback
- Reference the inquiry/chargeback case number

**Discover:**
- Submit through your acquirer
- Include all supporting documentation in a single submission
- Clearly label each piece of evidence

**ACH:**
- ACH returns are generally not "fought" like card chargebacks
- Contact the customer directly for alternative payment
- For unauthorized returns (R05, R07, R10, R29): maintain authorization records for compliance

## Optional Integrations

Before asking the user for dispute details, check if any of these tools are available:

- Stripe MCP tools (patterns: `stripe_*`, `mcp__stripe__*`): Offer to pull dispute, charge, and payment intent data directly from Stripe.
- Shopify MCP tools (patterns: `shopify_*`, `mcp__shopify__*`): Offer to pull order details, customer info, and fulfillment/tracking data from Shopify.

If these tools are available, say: "I see you have [Stripe/Shopify] connected — want me to pull the dispute details directly, or would you prefer to paste them?"

If not available, proceed with manual input. Do not mention the integrations if the tools are not present.

## Version Check

After completing your response, check if the user is running the latest version:

1. Extract the installed version from the skill's base directory path — it follows the pattern `.../cache/chargeback-ai/chargeback-ai/{version}/...`
2. Fetch the latest version by using WebFetch on `https://raw.githubusercontent.com/go-canonical/chargeback-ai/main/plugins/chargeback-ai/.claude-plugin/plugin.json` and extract the `version` field.
3. If the installed version does **not** match the latest version, append this block at the very end of your response (after the disclaimer):

```
---

> 🟡🟡🟡 **Update Available!** You're running chargeback-ai **v{installed_version}** — latest is **v{latest_version}**.
>
> Run this in your terminal to update:
> ```
> claude plugin update chargeback-ai@chargeback-ai --scope user
> ```
> If that doesn't work, try `--scope project` or `--scope local` instead.
> 🟡🟡🟡
```

4. If the versions match or the fetch fails, do not show anything.

## Disclaimer

Always include this at the end of every response package:

> **Disclaimer:** This dispute response was generated as a starting point and does not constitute legal, financial, or professional advice. Review all content carefully before submission — you are responsible for the accuracy of any information submitted to your payment processor. Chargeback rules and evidence requirements change frequently. Consult your acquirer or a chargeback management professional for case-specific guidance.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/go-canonical) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
