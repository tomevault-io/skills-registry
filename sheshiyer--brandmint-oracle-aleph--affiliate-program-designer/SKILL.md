---
name: affiliate-program-designer
description: Designs the commission structure and "Swipe Copy" for affiliates to use. Use when this capability is needed.
metadata:
  author: sheshiyer
---

# Affiliate Program Designer

This skill creates the toolkit that empowers partners to sell your product for you.

## Input Variables
- [PRODUCT_PRICE]
- [AD_COPY_ASSETS] (to reuse)

## The Protocol
1.  **Structure Design**: Define the offer.
    - Commission Rate (Standard vs. VIP).
    - Cookie Duration (e.g., 30 days).
    - Payout Terms.
2.  **Asset Creation (Swipe Files)**:
    - **Email Swipe**: Pre-written email for affiliates to send their list.
    - **Social Swipe**: Ready-to-post tweets/captions.
    - **Graphics**: Request banner sizes (using Visual Identity).
3.  **Onboarding Guide**: A "How-To" for new affiliates to get their link and start promoting.

## Output Instructions
Render into `templates/affiliate-toolkit.md`.


## Integration & Technical Specs

### API Specification
- **ID**: `affiliate-program-designer`
- **Path**: `skills/affiliate-program-designer/templates/affiliate-toolkit.md`
- **Context**: Part of *Social & Community*

### Data Flow
- **Input**: Derived from project context and upstream skills.
- **Output**: Generates `affiliate-toolkit.md`.

### CLI Usage
```bash
bun scripts/cli.ts activate affiliate-program-designer
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sheshiyer) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
