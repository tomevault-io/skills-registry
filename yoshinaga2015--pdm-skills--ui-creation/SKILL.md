---
name: ui-creation
description: Designs new UI flows and screens using conversion-oriented patterns from Flow knowhow. Use when creating new onboarding, pricing, paywall, checkout, or product detail UI, or when the user asks to draft UI structure, copy, CTA, and information hierarchy from scratch. Use when this capability is needed.
metadata:
  author: yoshinaga2015
---

# UI Creation Skill

## Purpose
Apply A/B testing insights to first-pass UI design so new screens are easy to understand, commercially effective, and trustworthy from day one.

## When to Use
- Building a brand-new screen or flow for a new feature
- Creating new onboarding, landing, pricing, paywall, checkout, or PDP UI
- Drafting initial options for CTA copy, icon choice, pricing presentation, or form structure

## Execution Steps
1. **Define the screen's primary job**
   - Narrow the goal to one core action (for example, start trial, complete purchase, finish onboarding).
2. **Separate essential information from noise**
   - Split content into `must-have` (decision-critical) and `nice-to-have` (supporting).
   - Design for both clarity and persuasion; treat them as separate quality checks.
3. **Reduce flow friction early**
   - Keep early steps lightweight, and move heavy inputs later when possible.
   - Prefer `smart default + editable later` when it fits the product.
4. **Build trust and risk reduction into the UI**
   - Make pricing terms, billing timing, cancellation, and return/refund conditions explicit.
   - Place trust signals (reviews, guarantees, evidence) near primary CTAs.
5. **Prepare two persuasive angles**
   - Feature-led vs benefit-led
   - Rational appeal vs emotional appeal
   - Action-focused CTA vs value-focused CTA
6. **Output A/B-ready variants**
   - Always provide 2-3 variants (A: baseline-friendly / B: strong change / C: balanced option).

## Default Guidelines by Screen Type

### Onboarding
- Minimize forced choices and mandatory upfront input; surface value first.
- If collecting multiple intents, avoid choice overload and reflect results immediately.
- For checklist-style flows, keep only actions tied to long-term retention.

### Pricing / Plan Selection
- Preserve decision-critical comparison points (feature gaps, limits, support terms).
- If emphasizing a default plan, keep alternative plans clearly accessible.
- If showing monthly breakdown pricing, also show total cost and contract terms.

### Paywall
- Design with hard-gate vs soft-gate variants in mind.
- Include gain-framed copy as a standard option.
- Use visuals to reinforce value context, not just decoration.

### Checkout
- Remove unnecessary account creation and heavy input before purchase.
- Improve visibility of payment options and discoverability of switching paths.
- Show transparency copy (returns, cancellation, billing conditions) early.

### PDP / LP
- Produce both feature-first and benefit-first versions.
- For dedicated landing pages, align messaging to traffic intent and reduce branching.
- Before adding new perks, improve visibility of existing benefits.

## Output Format
Always return UI creation proposals in this structure.

```markdown
## Goal
- [Primary user action this screen should drive]

## Target User and Context
- [Who arrives here and in what state]

## UI Variants
- Variant A: [Structure and intent]
- Variant B: [Structure and intent]
- Variant C (optional): [Balanced option]

## Copy and Elements
- Headline:
- Supporting copy:
- CTA:
- Trust signals:

## Expected Impact and Evaluation
- Primary metric: [CVR / completion rate / etc.]
- Secondary metrics: [drop-off / support tickets / retention / refunds / etc.]
- Guardrails: [Conditions that prevent trust or quality regression]
```

## Guardrails
- Do not ship a change based on revenue uplift alone; include quality metrics (churn/refund/NPS).
- Avoid dark patterns (misleading emphasis, hidden terms, or ambiguous conditions).
- Treat simplification as a method, not a license to remove decision-critical information.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/yoshinaga2015) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
