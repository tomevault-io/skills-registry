---
name: gemini-research
description: Iterative multi-agent research pipeline with gap analysis, source verification, and confidence-rated Obsidian reports. Uses parallel Gemini CLI agents + Exa with progressive deepening across multiple research waves. Use when this capability is needed.
metadata:
  author: codyrobertson
---

# Gemini Research Pipeline v2

## Overview

An iterative multi-agent research pipeline. Claude decomposes a topic, fans out parallel Gemini + Exa agents (Wave 1), performs gap analysis to find what's missing or contradictory, runs targeted follow-up agents (Wave 2), verifies key claims across independent sources, then synthesizes a confidence-rated report saved to Obsidian.

## Pipeline at a Glance

```
Step 0: Gather user intent
Step 1: Topic decomposition
Step 2: Wave 1 — Broad parallel research
Step 3: Gap analysis — Find holes, contradictions, thin areas
Step 4: Wave 2 — Targeted follow-up research
Step 5: Source verification — Cross-check key claims
Step 6: Synthesis — Merge, rate confidence, build structure
Step 7: Save to Obsidian — Rich formatting with callouts and badges
```

**Depth controls which stages run:**

| Depth | Stages | Behavior |
|-------|--------|----------|
| Quick | 0-2, 6-7 | Single wave, fast synthesis, no verification |
| Standard | 0-3, 5-7 | Single wave + gap analysis + verification, no Wave 2 |
| Deep | 0-7 (all) | Full iterative pipeline with two research waves |

---

## Step 0: Gather User Intent (Required)

**Before ANY research begins**, use AskUserQuestion to establish preferences. Ask all three in a single AskUserQuestion call with multiple questions:

```
Q1: "What should I title this research report?"
    Header: "Title"
    Options: [auto-generated suggestion, "Let me name it"]

Q2: "Where should I save it in Obsidian?"
    Header: "Location"
    Options: ["Research/ folder (default)", "Specify a different path"]

Q3: "How deep should the research go?"
    Header: "Depth"
    Options: [
      "Quick — Fast overview, single research wave",
      "Standard — Solid coverage with source verification (Recommended)",
      "Deep — Full iterative pipeline with gap analysis and follow-up waves"
    ]
```

Only proceed after all are answered.

---

## Step 1: Topic Decomposition

Claude breaks the research topic into focused, non-overlapping subtopics. Each subtopic must be:

- **Specific**: searchable as a standalone query
- **Distinct**: minimal overlap with other subtopics
- **Angled**: approaches the main topic from a unique perspective

Subtopic count by depth:

| Depth | Subtopics | Exa categories |
|-------|-----------|----------------|
| Quick | 2-3 | 1 |
| Standard | 4-5 | 2-3 |
| Deep | 6-8 | 3-4 |

Also identify:
- What **structured data** Exa should search for (companies, news, people, papers)
- What **time range** matters (last week, month, year, all time)
- Any **specific domains** to prioritize or exclude

Present the decomposition to the user before proceeding:

> "I've broken this into N subtopics: [list]. Starting research now."

---

## Step 2: Wave 1 — Broad Parallel Research

Launch **all agents in a single message** with multiple Task tool calls for true parallelism.

### Gemini Agents (one per subtopic)

Each Task agent (model: **haiku** for speed):

1. Formulates a focused research prompt for its subtopic
2. Pipes to Gemini CLI:
   ```bash
   export GEMINI_API_KEY="..." && gemini <<'PROMPT'
   You are a research analyst. Research the following topic using web grounding.

   TOPIC: [subtopic]
   CONTEXT: This is part of a broader investigation into [main topic].

   Requirements:
   1. Provide 8-12 key findings as bullet points
   2. For EVERY finding, include the source URL in parentheses
   3. Include direct quotes where they add value (with attribution)
   4. Note any data points, statistics, or metrics with their dates
   5. Flag anything that seems contradictory to common understanding
   6. Note the recency of your sources (date published if available)

   Format your response as:

   ## Findings
   - [finding] (source: URL)
   ...

   ## Key Data Points
   - [metric/stat]: [value] — [source, date]
   ...

   ## Contradictions or Surprises
   - [anything unexpected or conflicting]
   ...

   ## Source List
   1. [Title](URL) — [date if known] — [brief relevance note]
   ...
   PROMPT
   ```
3. Parses the response into structured JSON
4. Returns:
   ```json
   {
     "subtopic": "...",
     "findings": [
       {"claim": "...", "source_url": "...", "source_title": "...", "date": "...", "has_quote": false}
     ],
     "data_points": [
       {"metric": "...", "value": "...", "source": "...", "date": "..."}
     ],
     "contradictions": ["..."],
     "sources": [{"title": "...", "url": "...", "date": "...", "relevance": "..."}]
   }
   ```

**Critical**: Every finding MUST have a source URL. Unsourced findings are discarded.

### Exa Agents (parallel with Gemini)

Launch **separate Exa Task agents per category** for better coverage:

**Exa News Agent** (if news is relevant):
- Category: `news`
- 2-3 query variations
- `startPublishedDate` set to relevant time window
- Return: headlines, URLs, publication dates, summaries

**Exa Company Agent** (if companies are relevant):
- Category: `company`
- Returns rich metadata: headcount, funding, revenue, location
- Include `enableSummary: true` for context

**Exa People Agent** (if people/profiles matter):
- Category: `people`
- Public LinkedIn and profile data

**Exa Research Agent** (if academic/technical):
- Category: `research paper`
- Include `enableHighlights: true` with `highlightsQuery` set to main topic

Each Exa agent uses `mcp__exa__web_search_advanced_exa` with:
- `numResults`: 15-25 depending on depth
- `enableSummary: true`
- `contextMaxCharacters: 5000`
- 2-3 query variations merged and deduplicated

---

## Step 3: Gap Analysis (Standard + Deep only)

After all Wave 1 agents return, Claude reviews the collected findings and identifies:

### 3a. Coverage Gaps
- Subtopics where agents returned fewer than 3 findings
- Angles on the main topic that no subtopic addressed
- Time periods with no coverage

### 3b. Contradictions
- Findings from different agents that directly conflict
- Data points that disagree (e.g., different funding amounts)
- Claims that contradict well-known facts

### 3c. Unsubstantiated Claims
- Findings with only one source
- Claims that are significant but lack supporting data
- Surprising assertions that need independent confirmation

### 3d. Depth Gaps
- Areas where findings are surface-level (e.g., "X is growing" without numbers)
- Topics that deserve deeper exploration based on initial findings

Generate a structured gap report:

```json
{
  "coverage_gaps": ["...", "..."],
  "contradictions": [
    {"claim_a": "...", "source_a": "...", "claim_b": "...", "source_b": "..."}
  ],
  "needs_verification": ["claim 1", "claim 2"],
  "needs_depth": ["area 1", "area 2"],
  "follow_up_queries": ["specific query 1", "specific query 2"]
}
```

For **Standard depth**: Skip to Step 5 (verification) using this gap analysis to prioritize what to verify.
For **Deep depth**: Continue to Step 4 (Wave 2).

---

## Step 4: Wave 2 — Targeted Follow-Up (Deep only)

Launch targeted agents based on the gap analysis. These are **different from Wave 1** — they address specific holes, not broad subtopics.

### Gap-Filling Agents
For each coverage gap, launch a Gemini agent with a highly specific prompt:

```bash
export GEMINI_API_KEY="..." && gemini <<'PROMPT'
I'm researching [main topic] and found a gap in coverage around [specific gap].

Previous research established: [brief context from Wave 1]

Please research specifically:
- [targeted question 1]
- [targeted question 2]

Include source URLs for every finding.
PROMPT
```

### Contradiction-Resolution Agents
For each contradiction, launch an agent specifically to resolve it:

```bash
export GEMINI_API_KEY="..." && gemini <<'PROMPT'
I found conflicting information while researching [topic]:

Claim A: "[claim]" (source: [url])
Claim B: "[claim]" (source: [url])

Please determine:
1. Which claim is more accurate and why
2. Are both partially correct with different contexts?
3. What do the most authoritative sources say?

Include source URLs for your determination.
PROMPT
```

### Depth Agents
For areas needing more depth, launch agents with prompts that reference what Wave 1 found:

```bash
export GEMINI_API_KEY="..." && gemini <<'PROMPT'
Previous research found that [surface-level finding].

Go deeper:
- What are the specific numbers/metrics?
- What's the timeline of developments?
- Who are the key players involved?
- What are the second-order effects?

Include source URLs.
PROMPT
```

All Wave 2 agents launch in parallel in a single message.

---

## Step 5: Source Verification (Standard + Deep)

For **key claims** (the most important 8-12 findings from the research), run a verification pass.

Launch verification agents in parallel. Each agent takes one claim and checks it:

```bash
export GEMINI_API_KEY="..." && gemini <<'PROMPT'
VERIFICATION TASK

Claim to verify: "[claim]"
Original source: [url]

Please:
1. Search for this claim or closely related information
2. Find 2-3 independent sources that either confirm or contradict it
3. Rate your confidence: HIGH (3+ independent sources agree), MEDIUM (2 sources agree), LOW (only original source or contradicted)

Return:
- Confidence: HIGH/MEDIUM/LOW
- Supporting sources: [list with URLs]
- Contradicting sources: [list with URLs, if any]
- Notes: [any nuance or caveats]
PROMPT
```

Map verification results back to findings:

| Confidence | Badge | Meaning |
|------------|-------|---------|
| HIGH | `[✅ verified]` | 3+ independent sources corroborate |
| MEDIUM | `[🔶 likely]` | 2 sources agree, no contradictions |
| LOW | `[⚠️ unverified]` | Single source only |
| CONTRADICTED | `[❌ disputed]` | Sources actively disagree |

---

## Step 6: Synthesis

Claude (opus for Deep, sonnet for Standard/Quick) synthesizes all findings across all waves.

### 6a. Thematic Grouping
Organize findings by **theme**, not by subtopic or wave:
- Group related findings from different agents together
- Identify overarching narratives across themes

### 6b. Confidence Rating
Attach confidence badges from Step 5 to each finding. For Quick depth (no verification), default to `[🔶 likely]` for multi-source and `[⚠️ unverified]` for single-source.

### 6c. Comparative Tables
Where data supports it, build comparison tables:

```markdown
| Company | Funding | Headcount | Last Round |
|---------|---------|-----------|------------|
| Acme    | $50M    | 200       | Series B   |
| Beta    | $30M    | 150       | Series A   |
```

### 6d. Timeline Construction
If findings have temporal data, construct a timeline:

```markdown
> [!timeline]
> **2024 Q1** — Event A happened
> **2024 Q3** — Event B followed
> **2025 Q1** — Current state
```

### 6e. Contradictions Report
List any unresolved contradictions with both sides presented:

```markdown
> [!warning] Disputed Finding
> Source A claims X ([url]), while Source B claims Y ([url]).
> **Assessment**: [Claude's analysis of which is more credible and why]
```

---

## Step 7: Save to Obsidian

Use Obsidian MCP tools to write the report with rich formatting.

### Report Template

```markdown
---
title: "[Report Title]"
date: YYYY-MM-DD
type: research
topic: "[main topic]"
tags: [auto-generated tags]
sources_count: [number]
verified_count: [number of ✅ findings]
depth: quick|standard|deep
waves: 1|2
confidence_breakdown:
  high: [count]
  medium: [count]
  low: [count]
  disputed: [count]
---

# [Report Title]

> [!abstract] Executive Summary
> [2-3 paragraph synthesis of the most important findings. Lead with the
> single most significant insight. Include confidence context: "Based on
> N verified sources across M subtopics..."]

## Key Findings

### [Theme 1]
- [✅ verified] Finding with strong source support — [Source](url)
- [🔶 likely] Finding with moderate support — [Source](url)
- [⚠️ unverified] Finding from single source — [Source](url)

### [Theme 2]
- ...

> [!tip] Strongest Finding
> [Highlight the single best-supported, most impactful finding]

## Data & Statistics

| Metric | Value | Source | Date | Confidence |
|--------|-------|--------|------|------------|
| ...    | ...   | ...    | ...  | ✅/🔶/⚠️  |

## Comparative Analysis

[Tables comparing entities, products, companies, etc. where data supports it]

## Timeline

> [!timeline]
> [Chronological view if temporal data exists]

## Disputed Findings

> [!warning] [Claim]
> **Source A**: [claim] — [url]
> **Source B**: [counter-claim] — [url]
> **Assessment**: [analysis]

## Sources

### Verified Sources
1. [Title](URL) — [date] — [✅ verified, cited N times]

### Supporting Sources
2. [Title](URL) — [date] — [🔶 used for corroboration]

### Unverified Sources
3. [Title](URL) — [date] — [⚠️ single reference]

## Research Metadata
- **Date**: YYYY-MM-DD
- **Depth**: quick|standard|deep
- **Research waves**: 1 or 2
- **Total agents dispatched**: N
- **Subtopics researched**: [list]
- **Confidence breakdown**: X verified, Y likely, Z unverified, W disputed
- **Exa categories searched**: [list]
- **Gaps remaining**: [any known gaps that couldn't be filled]
```

Use `mcp__obsidian__write_note` with the path from Step 0.

After saving, confirm:

> "Report saved to [path] in Obsidian. [N] sources collected, [X] findings verified, [Y] disputed claims flagged. [Any notable gaps still remaining.]"

---

## Gemini CLI Invocation (Critical)

Always pipe prompts non-interactively:

```bash
export GEMINI_API_KEY="..." && gemini <<'PROMPT'
[prompt content]
PROMPT
```

**NEVER** use interactive mode. Always pipe input via heredoc.

The `GEMINI_API_KEY` must be exported in the same command since Task agents don't inherit env vars.

---

## Error Handling

- **gemini not found**: Tell user to run `setup.sh` or `npm install -g @google/gemini-cli`
- **Auth failure**: Tell user to set `GEMINI_API_KEY` or run `gemini` interactively once
- **Agent returns empty**: Retry once with simplified prompt. If still empty, log as coverage gap.
- **Obsidian MCP unavailable**: Fall back to writing the report as a local markdown file.
- **Partial agent failure**: Synthesize from successful agents. Note failed subtopics in Research Metadata.
- **Wave 2 failure**: Still produce report from Wave 1 data. Note in metadata that follow-up was incomplete.
- **Verification timeout**: Mark unverified findings as `[⚠️ unverified]` rather than blocking the report.

---

## Models

- **haiku**: Wave 1 + Wave 2 fan-out agents, verification agents (speed — Gemini does the heavy lifting)
- **sonnet**: Exa agents, gap analysis, Quick/Standard synthesis
- **opus**: Deep synthesis (6+ agent results, cross-wave merging, contradiction analysis)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/codyrobertson) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
