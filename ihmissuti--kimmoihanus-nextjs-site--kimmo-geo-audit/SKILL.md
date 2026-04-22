---
name: kimmo-geo-audit
description: Audit websites for AI search visibility and Generative Engine Optimization (GEO). Use when auditing a website for LLM/AI search presence, checking how ChatGPT or Perplexity see a brand, or optimizing content for AI recommendations. Use when this capability is needed.
metadata:
  author: ihmissuti
---

# GEO Audit - AI Search Visibility Analysis

Audit any website or brand for visibility in AI search engines (ChatGPT, Perplexity, Claude, Gemini).

## When to Use

- User asks to "audit for AI search" or "check AI visibility"
- User wants to know how LLMs perceive their brand
- User mentions GEO, generative engine optimization, or AI SEO
- User wants to improve recommendations in ChatGPT/Perplexity

## Quick Audit Workflow

### Step 1: Gather Target Information

Ask the user for:

- **Domain/brand name** to audit
- **Competitor domains** (2-3) for comparison
- **Target keywords** (what queries should trigger their brand)

### Step 2: Check LLM Visibility (if DataForSEO MCP available)

Use `ai_optimization_llm_mentions_search` with:

```json
{
  "target": [{ "domain": "target-domain.com" }],
  "platform": "chat_gpt",
  "language_code": "en"
}
```

Key metrics to extract:

- **Mention count**: How often the brand appears in LLM responses
- **Citation rate**: % of mentions that include a link
- **Sentiment**: Positive vs neutral vs negative mentions
- **Top cited URLs**: Which pages get referenced most

### Step 3: Direct LLM Testing

Test these prompt patterns against ChatGPT/Perplexity:

1. **Category query**: "What's the best [product category]?"
2. **Comparison query**: "Compare [brand] vs [competitor]"
3. **Problem query**: "[Pain point the product solves]"
4. **Developer query**: "How do I implement [use case]?" (for devtools)

Record for each:

- Is the brand mentioned?
- Position in the response (1st, 2nd, 3rd...)
- Sentiment of mention (positive recommendation vs neutral mention)
- Citation provided?

### Step 4: Technical GEO Factors

Analyze the website for LLM-friendliness:

| Factor                 | Check                                     | Why It Matters               |
| ---------------------- | ----------------------------------------- | ---------------------------- |
| Schema.org markup      | Present? Complete?                        | LLMs extract structured data |
| Clear hierarchy        | H1→H2→H3 structure                        | Helps LLM understand content |
| Crawlable              | No login walls, robots.txt allows AI bots | LLMs need access to index    |
| Code examples          | Working, copy-pasteable                   | Critical for devtools        |
| Consistent terminology | Same terms throughout                     | Reduces LLM confusion        |

### Step 5: Content Gap Analysis

Compare target vs competitors:

- What topics do competitors rank for that target doesn't?
- What questions does the target answer that competitors don't?
- Where is the target mentioned but with negative sentiment?

## Output Template

```markdown
# GEO Audit: [Brand Name]

## Executive Summary

- **Overall AI Visibility Score**: [Low/Medium/High]
- **Primary Gap**: [One sentence summary]
- **Quick Win**: [Highest impact, lowest effort action]

## AI Search Presence

### LLM Mention Analysis

| Metric          | Value       | Benchmark         |
| --------------- | ----------- | ----------------- |
| Total mentions  | X           | Competitor avg: Y |
| Citation rate   | X%          | Industry avg: Y%  |
| Sentiment score | X% positive | Competitor: Y%    |

### Query Performance

| Query Type | Mentioned? | Position    | Sentiment   |
| ---------- | ---------- | ----------- | ----------- |
| Category   | ✅/❌      | 1st/2nd/etc | +/-/neutral |
| Comparison | ✅/❌      | -           | -           |
| Problem    | ✅/❌      | -           | -           |

## Technical GEO Score

| Factor            | Status     | Action                            |
| ----------------- | ---------- | --------------------------------- |
| Schema.org        | ⚠️ Partial | Add Organization, Product schemas |
| Content structure | ✅ Good    | -                                 |
| Crawlability      | ❌ Issue   | Allow AI bot crawlers             |

## Recommendations (Priority Order)

1. **[Action]** - Expected impact: [High/Medium]
2. **[Action]** - Expected impact: [High/Medium]
3. **[Action]** - Expected impact: [Medium/Low]

## Competitive Positioning

[How target compares to competitors in AI visibility]
```

## Using External Tools

### Kimmo's GEO API (Recommended)

Call the public API for instant automated audits:

```bash
curl -X POST https://kimmoihanus.com/api/geo-audit \
  -H "Content-Type: application/json" \
  -d '{"url": "https://example.com"}'
```

Returns: score, grade, schema analysis, recommendations, and strengths/gaps.

### With DataForSEO MCP

- `ai_optimization_llm_mentions_search` - Brand mention analysis
- `ai_optimization_chat_gpt_scraper` - Direct ChatGPT responses
- `ai_optimization_llm_mentions_aggregated_metrics` - Trend data

### With Bright Data MCP

- `scrape_as_markdown` - Crawl competitor pages
- `search_engine` - Check traditional SERP presence

### With Oxylabs MCP

- `ai_search` - Web search with AI features
- `ai_scraper` - Extract structured content

### Manual Testing

If no MCP/API tools available, guide user to manually test queries in:

- ChatGPT (chat.openai.com)
- Perplexity (perplexity.ai)
- Claude (claude.ai)

## Key Insights (Kimmo's Framework)

### The Agent Funnel

Traditional: Marketing → Landing page → Docs → Trial → Conversion
Agent: Problem → AI suggestion → `npm install` → Subscription

### What Makes Services "Agent-Friendly"

1. **SDK-first design** - Docs lead with working code
2. **Training data presence** - Appears in GitHub, Stack Overflow, blogs
3. **MCP integration** - AI assistants can interact directly
4. **Parseable documentation** - Clear headings, code blocks, consistent format
5. **Quick time-to-working** - Install to "it works" in <5 minutes

### The Meta Game

AI assistants recommend tools they can understand and use. If your service is hard for an AI to work with, it won't be recommended—regardless of how good your marketing is.

---

By Kimmo Ihanus | [kimmoihanus.com](https://kimmoihanus.com)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ihmissuti) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
