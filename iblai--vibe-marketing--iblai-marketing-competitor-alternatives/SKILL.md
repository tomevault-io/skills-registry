---
name: iblai-marketing-competitor-alternatives
description: When the user wants to create competitor comparison or alternative pages for SEO and sales enablement. Also use when the user mentions 'alternative page,' 'vs page,' 'competitor comparison,' 'comparison page,' '[Product] vs [Product],' '[Product] alternative,' 'competitive landing pages,' 'how do we compare to X,' 'battle card,' or 'competitor teardown.' Use this for any content that positions your product against competitors. Covers four formats: singular alternative, plural alternatives, you vs competitor, and competitor vs competitor. For sales-specific competitor docs, see iblai-marketing-sales-enablement. Use when this capability is needed.
metadata:
  author: iblai
---

# /iblai-marketing-competitor-alternatives

Build competitor comparison and alternative pages that rank for competitive
search terms, help evaluators decide, and position your product clearly.

## Step 0: Context Check

Read `.agents/product-marketing-context.md` (or `.claude/product-marketing-context.md`
on older setups) first. Only ask for what isn't there.

Pull this context before writing:

1. **Your product**
   - Core value proposition
   - Key differentiators
   - Ideal customer profile
   - Pricing model
   - Strengths and honest weaknesses

2. **Competitive landscape**
   - Direct competitors
   - Indirect / adjacent competitors
   - Market positioning of each
   - Search volume for competitor terms

3. **Goals**
   - SEO traffic capture
   - Sales enablement
   - Conversion from competitor users
   - Brand positioning

---

## Core Principles

1. **Honesty builds trust.** Acknowledge competitor strengths. Be accurate about your limits. Don't misrepresent competitor features — evaluators verify.
2. **Depth over surface.** Go beyond feature checklists. Explain *why* a difference matters. Include use cases. Show, don't just tell.
3. **Help them decide.** Different tools fit different needs. Be clear who you're best for and who the competitor is best for. Reduce evaluation friction.
4. **Modular content.** Competitor data lives in one place. Updates propagate to every page. Single source of truth per competitor.

---

## Page Formats

### Format 1: [Competitor] Alternative (Singular)

**Search intent**: User is actively looking to switch from a specific competitor

**URL pattern**: `/alternatives/[competitor]` or `/[competitor]-alternative`

**Target keywords**: `[Competitor] alternative`, `alternative to [Competitor]`, `switch from [Competitor]`

**Page structure**:
1. Why people look for alternatives (validate their pain)
2. Summary: you as the alternative (quick positioning)
3. Detailed comparison (features, service, pricing)
4. Who should switch (and who shouldn't)
5. Migration path
6. Social proof from switchers
7. CTA

---

### Format 2: [Competitor] Alternatives (Plural)

**Search intent**: User is researching options, earlier in the journey

**URL pattern**: `/alternatives/[competitor]-alternatives`

**Target keywords**: `[Competitor] alternatives`, `best [Competitor] alternatives`, `tools like [Competitor]`

**Page structure**:
1. Why people look for alternatives (common pain points)
2. What to look for in an alternative (criteria framework)
3. List of alternatives (you first, but include real options)
4. Comparison table (summary)
5. Detailed breakdown of each
6. Recommendation by use case
7. CTA

Include 4-7 real alternatives. Being genuinely helpful builds trust and ranks better.

---

### Format 3: You vs [Competitor]

**Search intent**: User is directly comparing you to a specific competitor

**URL pattern**: `/vs/[competitor]` or `/compare/[you]-vs-[competitor]`

**Target keywords**: `[You] vs [Competitor]`, `[Competitor] vs [You]`

**Page structure**:
1. TL;DR summary (key differences in 2-3 sentences)
2. At-a-glance comparison table
3. Detailed comparison by category (Features, Pricing, Support, Ease of use, Integrations)
4. Who [You] is best for
5. Who [Competitor] is best for (be honest)
6. What customers say (testimonials from switchers)
7. Migration support
8. CTA

---

### Format 4: [Competitor A] vs [Competitor B]

**Search intent**: User comparing two competitors (not you directly)

**URL pattern**: `/compare/[competitor-a]-vs-[competitor-b]`

**Page structure**:
1. Overview of both products
2. Comparison by category
3. Who each is best for
4. The third option (introduce yourself)
5. Comparison table (all three)
6. CTA

Captures search traffic for competitor terms and positions you as the knowledgeable one in the category.

---

## Essential Sections

### TL;DR Summary
Open every page with a quick summary for scanners — key differences in 2-3 sentences.

### Paragraph Comparisons
Go beyond tables. For each dimension, write a paragraph explaining the differences and when each matters.

### Feature Comparison
Per category: describe how each handles it, list strengths and limits, give a bottom-line recommendation.

### Pricing Comparison
Tier-by-tier comparison, what's included, hidden costs, total cost for a sample team size.

### Who It's For
Explicit about ideal customer for each option. Honest recommendations build trust.

### Migration Section
What transfers, what needs reconfiguration, support offered, and quotes from customers who switched.

Detailed templates: [references/templates.md](references/templates.md).

---

## Content Architecture

### Centralized Competitor Data
One source of truth per competitor:
- Positioning and target audience
- Pricing (all tiers)
- Feature ratings
- Strengths and weaknesses
- Best for / not ideal for
- Common complaints (from reviews)
- Migration notes

Data structure and examples: [references/content-architecture.md](references/content-architecture.md).

---

## Research Process

### Deep Competitor Research

Per competitor:

1. **Product research** — sign up, use it, document features/UX/limits
2. **Pricing research** — current pricing, what's included, hidden costs
3. **Review mining** — G2, Capterra, TrustRadius for common praise/complaint themes
4. **Customer feedback** — talk to customers who switched (both directions)
5. **Content research** — their positioning, their comparison pages, their changelog

### Ongoing Updates

- **Quarterly** — verify pricing, check for major feature changes
- **When notified** — customer mentions a competitor change
- **Annually** — full refresh of all competitor data

---

## SEO Considerations

### Keyword Targeting

| Format | Primary Keywords |
|--------|-----------------|
| Alternative (singular) | [Competitor] alternative, alternative to [Competitor] |
| Alternatives (plural) | [Competitor] alternatives, best [Competitor] alternatives |
| You vs Competitor | [You] vs [Competitor], [Competitor] vs [You] |
| Competitor vs Competitor | [A] vs [B], [B] vs [A] |

### Internal Linking
- Link between related competitor pages
- Link from feature pages to relevant comparisons
- Create a hub page linking to all competitor content

### Schema Markup
FAQ schema for common questions like "What is the best alternative to [Competitor]?"

---

## Output Format

### Competitor Data File
Complete competitor profile in YAML for use across all comparison pages.

### Page Content
Per page: URL, meta tags, full page copy organized by section, comparison tables, CTAs.

### Page Set Plan
Recommended pages in priority order based on search volume.

---

## Task-Specific Questions

1. Common reasons people switch to you?
2. Customer quotes about switching?
3. Pricing vs. competitors?
4. Migration support offered?

---

## Related Skills

- **iblai-marketing-programmatic-seo**: Building competitor pages at scale
- **iblai-marketing-copywriting**: Compelling comparison copy
- **iblai-marketing-seo-audit**: Optimizing competitor pages
- **iblai-marketing-schema-markup**: FAQ and comparison schema
- **iblai-marketing-sales-enablement**: Internal sales collateral, decks, and objection docs

---
> Source: [iblai/vibe-marketing](https://github.com/iblai/vibe-marketing) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-23 -->
