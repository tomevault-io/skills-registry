---
name: contract-review
description: > Use when this capability is needed.
metadata:
  author: royjosephargumido
---

# Contract Review (PH/NZ Cross-Border)

Review legal contracts for risks, extract key terms, and suggest redlines. Focused on cross-border arrangements between New Zealand companies and Philippines-based workers, contractors, and service providers.

Built on the CUAD dataset (41 risk categories), ContractEval benchmarks, LegalBench, and the legislative frameworks in `references/legislation.md`.

## Step 1: Pre-Review Checklist

Before analyzing content, verify document completeness:

- [ ] Blank fields: Flag any "$X", "TBD", "[amount]", "____" placeholders.
- [ ] Missing exhibits: List all referenced schedules/exhibits and note which are missing.
- [ ] Signature status: Draft or already executed?
- [ ] All pages present: Check for truncation or missing sections.
- [ ] Currency: Are amounts in NZD, PHP, USD, or unspecified?
- [ ] Language: Is the contract in English? If bilingual, is there a prevailing language clause?

If blank fields or missing exhibits exist, flag prominently in the output header.

## Step 2: Identify Document Type and User Position

Ask if unclear: "Which party are you? (principal/company, contractor, employer, employee, customer, vendor, receiving party, disclosing party)"

This affects what counts as "risky":

- NZ company reviewing contractor agreement with PH contractor: flag terms that create employment indicators or expose the company to misclassification risk.
- NZ company as customer reviewing vendor SaaS agreement: flag vendor-favorable terms.
- NZ company reviewing NDA with PH party: flag enforceability concerns across jurisdictions.
- PH contractor reviewing agreement from NZ company: flag terms that limit contractor rights or create unfair obligations.

Assess power dynamic: NZ startup vs. large enterprise vendor (limited leverage), standard form vs. negotiated (some terms non-negotiable), regulated industry like healthcare/data (some terms legally required), cross-border enforceability (practical enforcement costs may exceed claim value).

## Step 3: Jurisdiction and Classification Check

Always determine early:

1. **Governing law:** Which country's law governs the agreement?
2. **Worker classification risk:** Could this contractor agreement be reclassified as employment under PH or NZ law? See `references/worker-classification.md`.
3. **Data privacy compliance:** Does the agreement involve personal data of PH citizens/residents, triggering the Data Privacy Act of 2012? See `references/data-privacy.md`.
4. **Healthcare data:** Does the agreement involve health information (classified as sensitive personal information under the PH DPA)?

## Red Flags Quick Scan

Check these danger signs FIRST before deep analysis:

| Red Flag | Why It Matters |
|----------|---------------|
| Employment indicators in contractor agreement | Misclassification exposes back pay, penalties, and "doing business" risk |
| Exclusivity clause for contractor | Strong employment indicator under PH four-fold test |
| Fixed hours/schedule for contractor | Control test indicator under PH and NZ law |
| Company equipment required | Economic dependence indicator |
| No explicit IP assignment | PH law does not assume work-for-hire for contractors |
| Missing DPA data provisions | Fines up to PHP 5M and imprisonment for non-compliance |
| No withholding tax clause | BIR requires income payor to withhold creditable tax |
| No SSS/PhilHealth/Pag-IBIG exclusion | Ambiguity increases reclassification risk |
| Liability cap < 6 months | Inadequate protection |
| Uncapped indemnification | Unlimited exposure |
| Unilateral amendment rights | Terms can change without consent |
| No termination for convenience | Locked in with no exit |
| Perpetual non-compete | Likely unenforceable in both PH and NZ |
| Offshore jurisdiction (neither PH nor NZ) | Expensive and impractical to enforce |
| "Sole discretion" language favoring counterparty | No objective standard |
| Asymmetric assignment rights | They can assign, you cannot |
| Missing breach notification timeline | DPA requires 72-hour notification |
| No governing law clause | Creates uncertainty in cross-border disputes |

## Output Format

Use markdown. Structure the review as follows (see full example in `references/output-example.md`):

```
# Contract Review: [Document Name]

**Document Type / Your Position / Counterparty / Governing Law / Risk Level / Document Status**

## Pre-Signing Alerts
(blank fields, missing exhibits, classification risk summary)

## Executive Summary
(2-3 sentence overview of key findings)

## Key Terms
(table: Term, Value, Location)

## Red Flags (Quick Scan)
(table: Flag, Found, Location)

## Risk Analysis
### CRITICAL
(detailed analysis with Issue, Consequence, Market Standard, Negotiability, Redline)

### IMPORTANT
(same format)

### Reviewed and Acceptable
(table of categories that passed review)

## Missing Provisions
(table: Provision, Priority, Why It Matters)

## Internal Consistency Issues
(any contradictions within the document)

## Negotiation Priority
(numbered table: Issue, Ask, Negotiability)
```

Risk levels: RED (Critical issues requiring immediate attention), YELLOW (Medium risk, review recommended), GREEN (Standard terms, low risk).

Always include the "Reviewed and Acceptable" section so the user knows what does not need attention.

## Document Type Checklists

Load the appropriate checklist from `references/checklists.md` based on document type:

- Independent Contractor / Consultancy Agreement
- Employment Agreement (NZ Company, PH-Based Employee)
- NDA (Cross-Border PH/NZ)
- SaaS / MSA

## Reference Files

Load these as needed during analysis:

- **`references/legislation.md`** — Complete legislative reference (PH, NZ, International)
- **`references/checklists.md`** — Document type checklists with all verification items
- **`references/worker-classification.md`** — PH four-fold test, NZ "real nature" test, provisions that reduce/increase risk
- **`references/data-privacy.md`** — PH DPA compliance, required DPA provisions, DSA, NPC registration, cross-border transfers
- **`references/risk-categories.md`** — CUAD 41 risk categories extended for PH/NZ
- **`references/market-standards.md`** — Market standard benchmarks and negotiability guide
- **`references/jurisdiction-notes.md`** — PH and NZ jurisdiction details, cross-border enforcement

## Guardrails

- **Not legal advice.** Recommend attorney review for material terms. Suggest PH counsel for labor/DPA matters and NZ counsel for employment/privacy matters.
- **Not tax advice.** Flag BIR and IRD obligations but do not calculate tax amounts.
- **Jurisdiction matters.** Always note when enforceability varies between PH and NZ. Highlight where PH mandatory protections override contractual choice of law.
- **Express uncertainty.** Say when interpretation is unclear or when PH/NZ law may conflict.
- **No hallucination.** Only reference text actually in the document. Only cite legislation listed in the legislative reference.
- **Show what is acceptable.** Always include "Reviewed and Acceptable" section.
- **Document status matters.** Note if already executed (review is informational only).
- **Classification risk is paramount.** Always assess worker classification risk for contractor agreements, even if the user does not ask.
- **Healthcare data sensitivity.** Always flag when agreements involve health data (heightened DPA requirements).
- **Cross-border complexity.** Always note when a provision may be interpreted differently under PH vs. NZ law.
- **NZ minimum entitlements.** Always flag when an employment agreement attempts to provide less than NZ statutory minimums. These cannot be contracted out of.
- **PH mandatory benefits.** Always flag when an employment agreement omits PH mandatory benefits (13th month pay, minimum wage, retirement pay, statutory leaves). These are non-negotiable.
- **Whistleblower protections.** Flag any contractual term that may conflict with NZ Protected Disclosures Act 2022.

*This review is for informational purposes only. Material terms should be reviewed by qualified legal counsel admitted in the relevant jurisdiction(s).*

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/royjosephargumido) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
