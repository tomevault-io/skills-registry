---
name: juma-seo-audit
description: Use when a client needs a comprehensive SEO audit, during onboarding to baseline organic search health, before building an SEO strategy, or when organic traffic has declined and root causes need to be identified.
metadata:
  author: just-marketing
---

# SEO Audit

## Overview

The SEO Audit produces a comprehensive, client-deliverable assessment of a website's search engine optimization health across four weighted pillars: Technical SEO (30%), On-Page SEO (25%), Content Analysis (25%), and Backlink Profile (20%). Each pillar is scored individually and rolled into an overall SEO Health Score. The audit identifies specific issues, quantifies their impact, benchmarks against competitors, and produces a prioritized action plan with estimated effort and impact for each recommendation.

This is not a checklist -- it is a strategic document that connects technical findings to business outcomes. Every issue is framed in terms of traffic, revenue, or competitive impact so that non-technical stakeholders understand the urgency and value of each fix.

## When to Use

- New client onboarding to establish an SEO baseline
- Organic traffic has declined and you need to diagnose the cause
- Before building or revising an SEO strategy or content roadmap
- Quarterly or semi-annual SEO health check
- After a site migration, redesign, or CMS change
- When preparing an SEO-focused proposal or upsell
- A client asks "why aren't we ranking for [keyword]?"
- Before a juma-geo-audit to establish traditional search performance first

## Prerequisites

- **juma-client-context** (required) -- brand identity, target audiences, business goals, and performance baselines
- **juma-competitor-intel** (recommended) -- competitor SEO profiles for benchmarking
- Access to SEO tools: Ahrefs, SEMrush, Moz, or equivalent (for keyword data, backlink analysis, site audit)
- Access to Google Search Console (for indexation, crawl errors, search performance data)
- Access to Google Analytics (GA4) for traffic and conversion data
- Access to PageSpeed Insights / Lighthouse for Core Web Vitals
- Full list of target keywords or willingness to build one during the audit
- Access to the site's CMS for on-page assessment (helpful but not required)

## Process

### Step 1: Crawl the Site

Run a comprehensive site crawl using Screaming Frog, Sitebulb, or equivalent:

1. Crawl all pages and capture HTTP status codes, redirects, canonical tags, and meta data
2. Identify crawl errors (4xx, 5xx), redirect chains, and orphan pages
3. Map the internal linking structure and identify link depth issues
4. Check XML sitemaps for completeness and errors
5. Review robots.txt for unintentional blocks
6. Note total crawlable pages vs. total indexed pages (Google Search Console)
7. Flag any JavaScript rendering issues that may prevent crawling

### Step 2: Analyze Technical SEO Factors

Evaluate each technical element and score its health. See [technical-seo-checklist.md](technical-seo-checklist.md) for the complete checklist with all items, output tables, and pass/fail targets.

Key areas to assess: Crawlability, Indexation, Site Speed & Core Web Vitals, Mobile-Friendliness, Structured Data, XML Sitemaps, Robots.txt, HTTPS & Security, URL Structure, International/Hreflang (if applicable), and Log File Analysis (if available).

### Step 3: Audit On-Page SEO

Review on-page elements across key page types (homepage, category pages, product/service pages, blog posts, landing pages):

1. **Title Tags:** Present, unique, within character limits, keyword-optimized, compelling for CTR
2. **Meta Descriptions:** Present, unique, within character limits, include CTAs, match search intent
3. **Heading Structure:** Proper H1 usage (one per page, keyword-relevant), logical H2-H6 hierarchy
4. **Internal Linking:** Link distribution, anchor text diversity, orphan pages, link depth from homepage
5. **Content Quality:** Depth, relevance, E-E-A-T signals (Experience, Expertise, Authoritativeness, Trustworthiness)
6. **Image Optimization:** Alt text present and descriptive, file sizes optimized, next-gen formats used
7. **Keyword Optimization:** Target keyword placement (title, H1, URL, first paragraph, body), keyword stuffing check
8. **User Intent Alignment:** Does the page content match what the searcher is looking for?

### Step 4: Perform Content Gap Analysis

Compare the client's content footprint against competitors and market opportunity:

1. **Keyword coverage audit:** Map existing content to target keywords. Identify keywords with content but poor rankings, and keywords with no content at all.
2. **Competitor content comparison:** Which topics do competitors rank for that the client does not cover?
3. **Content freshness:** Age of existing content. Flag pages that have not been updated in 12+ months.
4. **Thin content:** Pages with fewer than 300 words or low dwell time that may be dragging down site quality.
5. **Duplicate content:** Internal duplicates, near-duplicates, and cannibalization (multiple pages competing for the same keyword).
6. **Content cluster analysis:** Is the site organized around topic clusters with pillar pages? Or is content scattered without clear topical authority?
7. **SERP feature opportunities:** Which queries trigger featured snippets, People Also Ask, or other SERP features that the client could target?

### Step 5: Review Backlink Profile

Analyze the site's backlink profile for strength, health, and growth trajectory:

1. **Domain authority/rating:** Current score and trend over the last 12 months
2. **Total referring domains:** Count, growth rate, and comparison to competitors
3. **Link quality distribution:** Percentage of links from high-authority vs. low-authority domains
4. **Anchor text profile:** Natural distribution? Over-optimized? Brand vs. keyword anchors?
5. **Toxic links:** Identify spammy, irrelevant, or potentially harmful backlinks. Quantify risk level.
6. **Top linking pages:** Which client pages attract the most backlinks? Why?
7. **Competitor backlink comparison:** How does the client's link profile compare to top 3 competitors on volume, quality, and growth rate?
8. **Link gap analysis:** High-value domains that link to competitors but not the client
9. **Link acquisition rate:** New referring domains per month -- is it growing, flat, or declining?

### Step 6: Assess Local SEO (If Applicable)

If the client has physical locations or serves specific geographic areas:

1. **Google Business Profile:** Claimed, verified, complete, optimized?
2. **NAP consistency:** Name, address, phone consistent across directories?
3. **Local citations:** Present in major directories? Industry-specific directories?
4. **Review profile:** Volume, rating, recency, response strategy
5. **Local content:** Location pages, local landing pages, geo-modified keywords
6. **Local link building:** Links from local organizations, news, and community sites

### Step 7: Score and Prioritize

1. Score each pillar on a 0-100 scale based on findings
2. Apply weights and calculate the overall SEO Health Score -- see [scoring-rubric.md](scoring-rubric.md) for the complete weighted scoring system, severity-based point deductions, and score interpretation guide
3. Categorize every finding by severity (Critical / High / Medium / Low)
4. Estimate impact and effort for each recommendation
5. Build the prioritized action plan ordered by impact-to-effort ratio -- see [action-plan-template.md](action-plan-template.md) for the complete action plan structure with effort/impact estimation guides

## Output Format

```markdown
# SEO Audit: [Client Name]

**Prepared by:** [Agency Name]
**Date:** [Date]
**Website:** [URL]
**Period Analyzed:** [Date Range]

---

## Executive Summary

[2-3 paragraphs summarizing overall SEO health, the most critical findings, top opportunities, and estimated traffic/revenue impact of implementing recommendations. This section must stand alone for executive stakeholders.]

### SEO Health Score: [X/100]

See [scoring-rubric.md](scoring-rubric.md) for the weighted scoring table, severity-based point deductions, and score interpretation ranges.

### Critical Issues (Fix Immediately)

1. [Issue] -- [Impact in plain language]
2. [Issue] -- [Impact in plain language]
3. [Issue] -- [Impact in plain language]

### Top Opportunities

1. [Opportunity] -- [Estimated traffic/revenue impact]
2. [Opportunity] -- [Estimated traffic/revenue impact]
3. [Opportunity] -- [Estimated traffic/revenue impact]

---

## 1. Technical SEO (Score: [X/100])

See [technical-seo-checklist.md](technical-seo-checklist.md) for the complete technical SEO checklist with all subsection tables (Crawlability & Indexation, Site Speed & Core Web Vitals, Mobile-Friendliness, Structured Data, HTTPS & Security, URL Structure, International/Hreflang, Log File Analysis).

For each subsection, document:
- **Findings** with severity tags (CRITICAL / HIGH / MEDIUM / LOW)
- **Recommendations** with Effort and Impact levels

---

## 2. On-Page SEO (Score: [X/100])

### 2.1 Title Tags

| Issue | Count | Examples |
|-------|-------|---------|
| Missing | [X] | [URLs] |
| Duplicate | [X] | [URLs] |
| Too long (>60 chars) | [X] | [Examples] |
| Too short (<30 chars) | [X] | [Examples] |
| Missing target keyword | [X] | [Examples] |

### 2.2 Meta Descriptions

| Issue | Count | Examples |
|-------|-------|---------|
| Missing | [X] | [URLs] |
| Duplicate | [X] | [URLs] |
| Too long (>160 chars) | [X] | [Examples] |
| Missing CTA | [X] | [Examples] |

### 2.3 Heading Structure

- [H1 tag assessment: missing, duplicate, or multiple per page]
- [H2-H6 hierarchy assessment]
- [Keyword usage in headings]

### 2.4 Internal Linking

- **Average internal links per page:** [Number]
- **Orphan pages (no internal links):** [Count]
- **Pages with excessive links (>100):** [Count]
- **Average click depth from homepage:** [Number]
- **Pages more than 3 clicks from homepage:** [Count and list]

### 2.5 Image Optimization

| Issue | Count |
|-------|-------|
| Missing alt text | [X of Y images] |
| Oversized images (>200KB) | [X] |
| Not using next-gen formats | [X] |
| Missing lazy loading | [X] |

### 2.6 Content Quality Signals

- [E-E-A-T assessment]
- [Author attribution]
- [Content freshness signals]
- [User engagement signals from analytics]

**On-Page Recommendations:**
1. [Action] -- Effort: [Level] | Impact: [Level]
2. [Action] -- Effort: [Level] | Impact: [Level]
3. [Action] -- Effort: [Level] | Impact: [Level]

---

## 3. Content Analysis (Score: [X/100])

### 3.1 Content Inventory

| Metric | Value |
|--------|-------|
| Total indexed pages | [Number] |
| Blog posts / articles | [Number] |
| Service/product pages | [Number] |
| Landing pages | [Number] |
| Average content length | [Words] |
| Thin content pages (<300 words) | [Count] |
| Content updated in last 6 months | [Count / %] |

### 3.2 Keyword Coverage

| Keyword Category | Target Keywords | Ranking (Top 10) | Ranking (11-50) | Not Ranking |
|-----------------|----------------|------------------|-----------------|-------------|
| [Category 1, e.g., Brand] | [Count] | [Count] | [Count] | [Count] |
| [Category 2, e.g., Product] | [Count] | [Count] | [Count] | [Count] |
| [Category 3, e.g., Informational] | [Count] | [Count] | [Count] | [Count] |
| [Category 4, e.g., Commercial] | [Count] | [Count] | [Count] | [Count] |

### 3.3 Content Gaps vs. Competitors

| Topic/Keyword | Search Volume | [Comp 1] | [Comp 2] | [Comp 3] | [Client] | Priority |
|---------------|--------------|----------|----------|----------|----------|----------|
| [Topic 1] | [Volume] | [Rank] | [Rank] | [Rank] | [Not ranking] | [High/Med/Low] |
| [Topic 2] | [Volume] | [Rank] | [Rank] | [Rank] | [Not ranking] | [High/Med/Low] |
| [Topic 3] | [Volume] | [Rank] | [Rank] | [Rank] | [Not ranking] | [High/Med/Low] |

### 3.4 Content Quality Issues

| Issue | Pages Affected | Severity |
|-------|---------------|----------|
| Thin content (<300 words) | [Count and top examples] | [HIGH/MEDIUM/LOW] |
| Duplicate/near-duplicate | [Count and examples] | [HIGH/MEDIUM/LOW] |
| Keyword cannibalization | [Keyword: Pages competing] | [HIGH/MEDIUM/LOW] |
| Outdated content (>12 months) | [Count] | [HIGH/MEDIUM/LOW] |
| Missing internal links | [Count] | [HIGH/MEDIUM/LOW] |

### 3.5 SERP Feature Opportunities

| SERP Feature | Current Wins | Opportunities | Action Required |
|-------------|-------------|---------------|-----------------|
| Featured Snippets | [Count] | [Keywords with potential] | [Content format needed] |
| People Also Ask | [Count] | [Questions to target] | [FAQ content needed] |
| Image Pack | [Count] | [Keywords with potential] | [Image optimization needed] |
| Video | [Count] | [Keywords with potential] | [Video content needed] |

**Content Recommendations:**
1. [Action] -- Effort: [Level] | Impact: [Level]
2. [Action] -- Effort: [Level] | Impact: [Level]
3. [Action] -- Effort: [Level] | Impact: [Level]

---

## 4. Backlink Profile (Score: [X/100])

### 4.1 Overview

| Metric | [Client] | [Comp 1] | [Comp 2] | [Comp 3] |
|--------|----------|----------|----------|----------|
| Domain Authority/Rating | [Score] | [Score] | [Score] | [Score] |
| Referring Domains | [Count] | [Count] | [Count] | [Count] |
| Total Backlinks | [Count] | [Count] | [Count] | [Count] |
| New Referring Domains/Month | [Count] | [Count] | [Count] | [Count] |
| Avg. DR of Linking Domains | [Score] | [Score] | [Score] | [Score] |

### 4.2 Link Quality Distribution

| Quality Tier | Count | % of Total |
|-------------|-------|-----------|
| High Authority (DR 60+) | [Count] | [%] |
| Medium Authority (DR 30-59) | [Count] | [%] |
| Low Authority (DR 1-29) | [Count] | [%] |

### 4.3 Anchor Text Profile

| Anchor Type | % of Links | Assessment |
|-------------|-----------|------------|
| Brand name | [%] | [Healthy/Over/Under] |
| Exact match keyword | [%] | [Healthy/Over/Under] |
| Partial match keyword | [%] | [Healthy/Over/Under] |
| Generic (click here, etc.) | [%] | [Healthy/Over/Under] |
| URL/naked link | [%] | [Healthy/Over/Under] |

### 4.4 Toxic Links

- **Potentially toxic links identified:** [Count]
- **Risk level:** [High/Medium/Low]
- **Recommendation:** [Disavow / Monitor / No action needed]
- **Top toxic sources:** [List with reasons]

### 4.5 Link Gap Analysis

| Domain (links to competitors, not client) | DR | [Comp 1] | [Comp 2] | [Comp 3] | Outreach Priority |
|------------------------------------------|-----|----------|----------|----------|-------------------|
| [Domain 1] | [DR] | [Yes/No] | [Yes/No] | [Yes/No] | [High/Med/Low] |
| [Domain 2] | [DR] | [Yes/No] | [Yes/No] | [Yes/No] | [High/Med/Low] |
| [Domain 3] | [DR] | [Yes/No] | [Yes/No] | [Yes/No] | [High/Med/Low] |

**Backlink Recommendations:**
1. [Action] -- Effort: [Level] | Impact: [Level]
2. [Action] -- Effort: [Level] | Impact: [Level]
3. [Action] -- Effort: [Level] | Impact: [Level]

---

## 5. Local SEO (Score: [X/100] or N/A)

*Include this section only if the client has physical locations or serves specific geographic areas.*

| Factor | Status | Notes |
|--------|--------|-------|
| Google Business Profile | [Claimed/Unclaimed/Incomplete] | [Details] |
| NAP Consistency | [Consistent/Inconsistent] | [Issues found] |
| Local Citations | [Count in top directories] | [Missing from: ...] |
| Reviews (Google) | [Count, Avg Rating] | [Response rate, recency] |
| Local Content | [Present/Missing] | [Location pages assessment] |
| Local Backlinks | [Count] | [Quality assessment] |

---

## Prioritized Action Plan

See [action-plan-template.md](action-plan-template.md) for the complete action plan structure with Critical/High/Medium/Low priority tables, effort estimation guide, and impact estimation guide.

---

## Appendix: Data Sources & Methodology

- **Crawl Tool:** [Tool name, date, settings]
- **SEO Platform:** [Tool name, date]
- **Google Search Console:** [Date range, property verified]
- **Google Analytics:** [Date range, property]
- **PageSpeed Insights:** [Date tested, pages tested]
- **Backlink Data:** [Tool name, date]
- **Scoring Methodology:** Pillar scores are calculated based on the weighted severity of issues found. Critical issues deduct [X] points, High deduct [X], Medium deduct [X], Low deduct [X]. The overall score applies pillar weights (Technical 30%, On-Page 25%, Content 25%, Backlinks 20%).
```

## Common Mistakes

- **Reporting issues without business impact** -- "37 images are missing alt text" means nothing to a CMO. "37 images missing alt text are reducing image search visibility, which accounts for an estimated [X] monthly sessions for competitors in this space" drives action.
- **Treating all issues as equal severity** -- Not every SEO issue matters equally. A crawl-blocking robots.txt error is critical. A meta description that is 5 characters too long is low priority. Use the severity and impact scoring consistently.
- **Auditing without benchmarking** -- Raw scores are meaningless without context. Always compare against competitors and industry benchmarks so the client understands whether a score of 62/100 means they are ahead or behind.
- **Ignoring search intent** -- Ranking for a keyword is pointless if the page does not match what the searcher wants. Every content recommendation should include the intent type (informational, navigational, commercial, transactional) and ensure the content format matches.
- **Technical-only audit** -- Many SEO audits over-index on technical issues and under-invest in content and backlink analysis. Content gaps and thin content often represent larger ranking opportunities than fixing minor technical issues.
- **One-time audit without a roadmap** -- The audit must end with a prioritized, time-bound action plan. A 40-page audit that does not tell the client what to do first, second, and third will sit on a shelf.
- **Forgetting to check the competitive backlink gap** -- The link gap analysis often surfaces the highest-value link building opportunities. It answers "who already links to my competitors and might link to me?"
- **Not connecting SEO findings to revenue** -- Wherever possible, translate traffic estimates into revenue estimates using the client's conversion rate and average order value or deal size. This makes the business case for investment.

## Related Skills

- **juma-client-context** -- provides business goals, target audiences, and performance baselines that scope the audit
- **juma-competitor-intel** -- feeds competitor SEO data into benchmark comparisons and gap analysis
- **juma-geo-audit** -- extends the SEO audit into AI search visibility and citation optimization
- **juma-content-calendar** -- content gap findings from the audit directly populate the content roadmap
- **juma-channel-audit** -- the organic search assessment in the channel audit references SEO audit findings
- **juma-cro-audit** -- SEO drives traffic, CRO converts it; these audits work in tandem
- **juma-proposal** -- SEO audit findings strengthen the situation analysis and justify investment tiers
- **juma-reporting** -- ongoing SEO reporting tracks progress against the audit's prioritized action items

---
> Source: [just-marketing/agency-skills](https://github.com/just-marketing/agency-skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-23 -->
