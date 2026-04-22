---
name: geo-checklist-auditor
description: Audit pages against the 12-step GEO best practices checklist. Use when reviewing content for AI search readiness, ensuring pages follow Generative Engine Optimization principles, or preparing content for better visibility in ChatGPT, Perplexity, and Google AI Mode. Use when this capability is needed.
metadata:
  author: ihmissuti
---

# GEO Checklist Auditor

Audit web pages against the comprehensive GEO (Generative Engine Optimization) best practices checklist to ensure optimal visibility in AI search engines.

## Why This Audit Matters

- AI-sourced visitors convert at 27% vs 2.1% from traditional search (12x improvement)
- 60% of queries now end in zero-click answers
- Only 16% of brands systematically track AI search performance
- Brands with 70%+ compliance on GEO checklists see measurably higher citation rates

## The 12-Step GEO Audit Checklist

Use this checklist as a comprehensive pass before publishing or updating any high-value page.

### 1. AI Search Visibility Baseline

**What to check:**

- Has the page topic been tested in AI assistants?
- Which brands currently appear for related queries?
- What sources are being cited?

**Audit actions:**

- [ ] Test 3-5 relevant prompts in ChatGPT
- [ ] Test same prompts in Perplexity
- [ ] Test in Google AI Mode if available
- [ ] Document which competitors are mentioned
- [ ] Note which sources are cited

**Test query patterns:**

```
"What are the best [your category]?"
"How does [your product type] work?"
"[Your product] vs [competitor] comparison"
"What companies offer [your service]?"
```

### 2. Business KPI Alignment

**What to check:**

- Are GEO objectives connected to revenue metrics?
- Can improvements be measured against business outcomes?

**Audit questions:**

- [ ] Page purpose aligns with business objectives
- [ ] Success metrics defined (visibility, citations, conversions)
- [ ] Tracking in place to measure impact
- [ ] Page targets specific customer journey stage

### 3. Technical Infrastructure

**What to check:**

- Can AI crawlers access and parse the page?
- Does the page meet performance requirements?

**Technical checklist:**

- [ ] AI crawlers allowed in robots.txt (GPTBot, ClaudeBot, PerplexityBot)
- [ ] llms.txt implemented at domain root
- [ ] Server-side rendering in place
- [ ] First Contentful Paint < 0.4s (target for AI citation eligibility)
- [ ] Core Web Vitals passing (LCP < 2.5s, FID < 100ms, CLS < 0.1)
- [ ] Mobile-friendly layout
- [ ] No JavaScript-dependent critical content

### 4. Schema Markup Implementation

**What to check:**

- Is appropriate schema markup present?
- Is structured data valid?

**Schema audit by content type:**

| Content Type | Required Schema | Present? | Valid? |
| ------------ | --------------- | -------- | ------ |
| Article/Blog | Article         | ☐        | ☐      |
| FAQ section  | FAQPage         | ☐        | ☐      |
| Tutorial     | HowTo           | ☐        | ☐      |
| Product page | Product         | ☐        | ☐      |
| Company page | Organization    | ☐        | ☐      |
| Author page  | Person          | ☐        | ☐      |

**Schema checklist:**

- [ ] Schema type matches content type
- [ ] Required properties populated
- [ ] dateModified reflects actual last update
- [ ] JSON-LD syntax is valid
- [ ] No conflicting schema on page

### 5. Content Extractability

**What to check:**

- Can AI easily extract key information?
- Is content structured for machine reading?

**Structure checklist:**

- [ ] Clear H1 matching primary intent
- [ ] Logical H2/H3 hierarchy
- [ ] TL;DR or summary section at top
- [ ] Each section leads with direct answer
- [ ] Paragraphs average 2-4 sentences
- [ ] Key data in tables or callouts
- [ ] Processes use numbered lists
- [ ] Features use bullet points
- [ ] Each paragraph is quotable standalone

**The 0.4s Speed Signal:**
Fast-loading pages are 3x more likely to be cited by ChatGPT. Check that content is in initial HTML, not JavaScript-rendered.

### 6. Question-Based Architecture

**What to check:**

- Does content address real user prompts?
- Are all funnel stages covered?

**Query intent mapping:**

| Stage         | Prompt Pattern                   | Page Addresses? |
| ------------- | -------------------------------- | --------------- |
| Awareness     | "What is [topic]?"               | ☐ Yes ☐ No      |
| Awareness     | "How does [topic] work?"         | ☐ Yes ☐ No      |
| Consideration | "Best [category] for [use case]" | ☐ Yes ☐ No      |
| Consideration | "[Product A] vs [Product B]"     | ☐ Yes ☐ No      |
| Decision      | "[Product] pricing"              | ☐ Yes ☐ No      |
| Decision      | "[Product] reviews"              | ☐ Yes ☐ No      |

**Content checklist:**

- [ ] H2 headings match real question patterns
- [ ] FAQ section addresses common questions
- [ ] Content covers query fan-out variations
- [ ] Headings mirror how users phrase questions

### 7. E-E-A-T Authority Signals

**What to check:**

- Is expertise demonstrated?
- Are claims supported by evidence?

**Authority checklist:**

- [ ] Author credentials visible
- [ ] Author page exists with schema
- [ ] Content includes original insights
- [ ] Claims backed by data/sources
- [ ] External citations to authoritative sources
- [ ] Statistics from credible sources (within last 2 years)
- [ ] Expert quotes where relevant

**Evidence requirements by content length:**

| Article Length | External Stats Needed |
| -------------- | --------------------- |
| Under 2k words | 3 stats               |
| 2-3k words     | 4-5 stats             |
| 3-5k words     | 5-7 stats             |
| 5k+ words      | 7-10 stats max        |

### 8. Web Mentions Strategy

**What to check:**

- Does brand appear on third-party sources?
- Is information consistent across sources?

Web mentions correlate 3x more strongly with AI visibility than backlinks (0.664 vs 0.218 correlation).

**Third-party presence audit:**

- [ ] Wikipedia/Wikidata presence (if notable)
- [ ] Industry directory listings
- [ ] Review site profiles (G2, Capterra, Trustpilot)
- [ ] LinkedIn company page
- [ ] Relevant Reddit communities
- [ ] YouTube presence
- [ ] Industry forums/communities

**Consistency check:**

- [ ] Company description consistent across platforms
- [ ] Product information matches website
- [ ] Pricing is current everywhere
- [ ] Contact information is accurate

### 9. Customer Journey Mapping

**What to check:**

- Is content aligned with specific journey stages?
- Are journey triggers addressed?

**Journey stage requirements:**

| Stage         | Query Type                    | Content Requirements                    |
| ------------- | ----------------------------- | --------------------------------------- |
| Awareness     | Problem-focused, broad        | Comprehensive overviews, trend analysis |
| Consideration | Solution-focused, comparative | Comparison content, evaluation criteria |
| Decision      | Brand-focused, specific       | Product info, social proof, pricing     |

**Checklist:**

- [ ] Page serves clear journey stage
- [ ] Content addresses stage-appropriate questions
- [ ] CTAs match journey stage intent
- [ ] Internal links guide to next stage

### 10. AI-Specific Tracking

**What to check:**

- Are AI visibility metrics being tracked?
- Can improvements be measured?

**Metrics to establish:**

- [ ] Brand visibility baseline documented
- [ ] Citation rate tracked
- [ ] AI Share of Voice vs competitors measured
- [ ] AI referral traffic identified in analytics
- [ ] Conversion rate for AI traffic tracked

### 11. Common Mistakes Avoided

**Verify the page avoids these issues:**

- [ ] NOT keyword stuffing (AI penalizes manipulation)
- [ ] NOT thin content (< 500 words for important pages)
- [ ] NOT using only one distribution channel
- [ ] NOT measuring only traditional SEO metrics
- [ ] NOT ignoring AI bot access in robots.txt
- [ ] NOT using JavaScript-only content rendering
- [ ] NOT having slow page load times (> 0.4s FCP)
- [ ] NOT missing schema markup
- [ ] NOT having inconsistent brand information
- [ ] NOT neglecting third-party presence
- [ ] NOT publishing static content without refresh plan

### 12. Continuous Optimization Plan

**What to check:**

- Is there a plan for ongoing updates?
- Are refresh triggers defined?

**Refresh cadence by content type:**

| Content Type         | Refresh Every        |
| -------------------- | -------------------- |
| Product pages        | Monthly              |
| Pricing pages        | Monthly              |
| Industry guides      | 60-90 days           |
| Blog posts with data | Quarterly            |
| Documentation        | When features change |
| Evergreen content    | 6 months             |

**Optimization checklist:**

- [ ] Last update date is visible
- [ ] Statistics are current (< 12 months old)
- [ ] Examples are relevant and recent
- [ ] Links are working (no 404s)
- [ ] Product information is accurate
- [ ] Pricing is current
- [ ] Next refresh date scheduled

## Audit Scoring

### Calculate Overall GEO Score

| Section                     | Max Points | Score      |
| --------------------------- | ---------- | ---------- |
| 1. Visibility Baseline      | 10         | \_\_\_     |
| 2. KPI Alignment            | 10         | \_\_\_     |
| 3. Technical Infrastructure | 10         | \_\_\_     |
| 4. Schema Markup            | 10         | \_\_\_     |
| 5. Content Extractability   | 10         | \_\_\_     |
| 6. Question Architecture    | 10         | \_\_\_     |
| 7. E-E-A-T Signals          | 10         | \_\_\_     |
| 8. Web Mentions             | 10         | \_\_\_     |
| 9. Journey Mapping          | 10         | \_\_\_     |
| 10. AI Tracking             | 5          | \_\_\_     |
| 11. Mistakes Avoided        | 10         | \_\_\_     |
| 12. Optimization Plan       | 5          | \_\_\_     |
| **Total**                   | **100**    | **\_\_\_** |

### Score Interpretation

| Score  | Rating    | Action                          |
| ------ | --------- | ------------------------------- |
| 90-100 | Excellent | Maintain and monitor            |
| 75-89  | Good      | Minor optimizations needed      |
| 60-74  | Fair      | Significant improvements needed |
| 40-59  | Poor      | Major restructuring required    |
| < 40   | Critical  | Full content overhaul needed    |

**Target: 70%+ compliance** for effective AI visibility.

## Audit Report Template

```markdown
# GEO Audit Report

**Page:** [URL]
**Date:** [Date]
**Auditor:** [Name]
**Overall Score:** [X/100]

## Executive Summary

[2-3 sentence summary of findings]

## Critical Issues (Fix Immediately)

1. [Issue with impact]
2. [Issue with impact]

## High Priority (Fix This Week)

1. [Issue with recommendation]
2. [Issue with recommendation]

## Medium Priority (Fix This Month)

1. [Issue with recommendation]
2. [Issue with recommendation]

## Strengths

- [What's working well]
- [What's working well]

## Section Scores

| Section                  | Score | Status |
| ------------------------ | ----- | ------ |
| Visibility Baseline      | X/10  | ✓/✗    |
| Technical Infrastructure | X/10  | ✓/✗    |
| Schema Markup            | X/10  | ✓/✗    |
| Content Extractability   | X/10  | ✓/✗    |
| ...                      | ...   | ...    |

## Recommendations

1. [Specific actionable recommendation]
2. [Specific actionable recommendation]
3. [Specific actionable recommendation]

## Next Review Date

[Date for follow-up audit]
```

## Quick Audit Checklist

For fast assessments, use this condensed 10-point check:

- [ ] **Structure:** H1 matches intent, logical H2/H3 hierarchy
- [ ] **Answer First:** Main point in first 1-2 sentences of each section
- [ ] **TL;DR:** Summary section at page top
- [ ] **FAQ:** Common questions addressed with FAQ schema
- [ ] **Schema:** Appropriate type implemented, valid syntax
- [ ] **Speed:** Page loads under 0.4s FCP
- [ ] **Freshness:** Updated within 90 days
- [ ] **Entities:** Consistent terminology throughout
- [ ] **Evidence:** Claims backed by data/sources
- [ ] **Quotability:** Each paragraph stands alone

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ihmissuti) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
