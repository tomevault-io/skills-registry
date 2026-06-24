---
name: last-30-days
description: Research any topic from the last 30 days using X, Reddit, and web sources. Use when you need current trends, recent discussions, or up-to-date information on any subject. Use when this capability is needed.
metadata:
  author: az9713
---

# Last 30 Days Research Skill

Research any topic using recent data from X/Twitter, Reddit, and web sources. This skill discovers current best practices, frameworks, and techniques through live research.

## Usage

```
/last-30-days <topic>
```

## Arguments

- `$ARGUMENTS`: The topic to research (e.g., "cold email frameworks", "AI coding assistants", "growth hacking strategies")

## Workflow

### Phase 1: Parallel Research

Run all three searches simultaneously to gather comprehensive data:

1. **X/Twitter Search** (via XAI Grok API):
   ```bash
   python .claude/skills/last-30-days/scripts/search_x.py "$ARGUMENTS"
   ```

2. **Reddit Search** (via OpenAI API):
   ```bash
   python .claude/skills/last-30-days/scripts/search_reddit.py "$ARGUMENTS"
   ```

3. **Web Search** (via WebSearch tool):
   Use the WebSearch tool to find recent articles, blog posts, and discussions about the topic.

### Phase 2: Synthesis

After gathering results from all three sources:

1. **Identify Key Patterns**: Look for recurring themes, frameworks, and strategies mentioned across sources
2. **Extract Actionable Insights**: Distill findings into practical, applicable knowledge
3. **Note Top Sources**: Highlight influential accounts, subreddits, and websites
4. **Summarize Trends**: Identify what's currently working and what's emerging
5. **Track Source Attribution**: For every insight, note which source it came from (X, Reddit, or Web) for the audit trail

### Phase 3: Present Findings

Structure the output as:

```
## Research Summary: [Topic]

### Key Discoveries
- [Framework/technique 1] `[source: X|Reddit|Web]`
- [Framework/technique 2] `[source: X|Reddit|Web]`
- [Trend or insight] `[source: X|Reddit|Web]`

### From X/Twitter
- Top voices: [accounts discussing this]
- Key posts: [notable discussions]
- Engagement patterns: [what resonates]

### From Reddit
- Active subreddits: [relevant communities]
- Top discussions: [popular threads]
- Community sentiment: [overall opinion]

### From Web
- Recent articles: [notable publications]
- Expert opinions: [thought leaders]
- Emerging trends: [what's new]

### Actionable Takeaways
1. [Specific recommendation]
2. [Strategy to apply]
3. [Framework to use]

### Sources
- [Source Title](URL)
- [Source Title](URL)

---

## Audit Trail

### X/Twitter Sources
| Insight | Author/Account | Post URL | Date |
|---------|----------------|----------|------|
| [Finding] | @handle | [link] | YYYY-MM-DD |

### Reddit Sources
| Insight | Subreddit | Thread Title | URL | Upvotes |
|---------|-----------|--------------|-----|---------|
| [Finding] | r/subreddit | [title] | [link] | N |

### Web Sources
| Insight | Publication | Article Title | URL | Date |
|---------|-------------|---------------|-----|------|
| [Finding] | [site] | [title] | [link] | YYYY-MM-DD |
```

**Audit Trail Guidelines:**
- Every key discovery and actionable takeaway should be traceable to at least one source
- Include direct URLs whenever possible so users can verify and explore further
- For X/Twitter: capture the @handle and link to the specific post
- For Reddit: include subreddit, thread title, and upvote count as credibility signal
- For Web: note the publication name, article title, and publication date
- If a source search fails (e.g., API error), note this in the audit trail section

### Phase 4: Save Results

After presenting findings, save the research to a markdown file:

1. **Generate filename**: Use the topic to create a descriptive filename (e.g., `ai-coding-assistants-research-2026.md`)
2. **Add metadata**: Include the research date at the top of the file
3. **Include full audit trail**: The saved file MUST include the complete audit trail tables so users can trace any insight back to its original source
4. **Save location**: Save to `output/` in the project root directory
5. **Confirm to user**: Let the user know where the file was saved

## Follow-up Application

After research is complete, the user can give minimal context and Claude will apply the discovered knowledge:

**Example Flow:**
```
User: /last-30-days highest performing cold email frameworks
[Skill discovers: AIDA, Three Ps, Intention Data Triggers]

User: Write me cold emails for getting on Greg's podcast. I once made a smart oven.
[Claude applies discovered frameworks without user reading research]
```

## Environment Requirements

### API Keys

The Python scripts require these environment variables:
- `XAI_API_KEY`: API key for X.AI (Grok) - get from https://console.x.ai
- `OPENAI_API_KEY`: API key for OpenAI

Set these in a `.env` file in the project root or as system environment variables.

### Python Dependencies

Install required packages:
```bash
pip install -r .claude/skills/last-30-days/scripts/requirements.txt
```

Key dependencies:
- `xai-sdk>=1.3.1` - Required for X/Twitter search (Agent Tools API)
- `openai>=1.0.0` - Required for Reddit search
- `python-dotenv>=1.0.0` - For loading environment variables

## Troubleshooting

### General Issues

1. Ensure dependencies are installed: `pip install -r .claude/skills/last-30-days/scripts/requirements.txt`
2. Verify API keys are set correctly
3. Check API rate limits and quotas

### X/Twitter Search Errors

#### Error: "Your team doesn't have any credits or licenses"

```
PERMISSION_DENIED: Your newly created team doesn't have any credits or licenses yet.
```

**Solution**: Purchase credits at the xAI console URL provided in the error message.

#### Error: "Live search is deprecated" (HTTP 410)

```
Error code: 410 - {'error': 'Live search is deprecated. Please switch to the Agent Tools API'}
```

**Background**: In late 2025, xAI deprecated the Live Search API (which used `search_parameters` in the Chat Completions endpoint) in favor of the new Agent Tools API (Responses API).

**Solution**: The script has been updated to use the native `xai-sdk` package with the `x_search()` tool. Ensure you have `xai-sdk>=1.3.1` installed.

#### Error: "unknown variant `search`, expected `function` or `live_search`"

```
Failed to deserialize the JSON body: tools[0].type: unknown variant `search`
```

**Background**: This error occurred when using the OpenAI-compatible endpoint with incorrect tool type values.

**Solution**: The X/Twitter search now uses the native xAI SDK with the Responses API, which handles tool configuration automatically.

---

## Changelog

### 2026-02-04

**X/Twitter Search Migration to Agent Tools API**

- **Problem**: The original implementation used the OpenAI-compatible Chat Completions endpoint with `search_parameters`, which was deprecated by xAI in December 2025.

- **Root Cause Analysis**:
  1. Initial code used `"type": "search"` - invalid tool type
  2. Changed to `"type": "live_search"` - missing required `sources` field
  3. Added `search_parameters` with `sources` - but Chat Completions Live Search API returned HTTP 410 (deprecated)

- **Solution**: Migrated from `openai` client to native `xai-sdk` package using the Responses API with `x_search()` agent tool.

- **Changes**:
  - Rewrote `scripts/search_x.py` to use `xai-sdk` Client and `x_search()` tool
  - Added `xai-sdk>=1.3.1` to `requirements.txt`
  - Updated model from `grok-3-latest` to `grok-4-1-fast` (recommended for agent tools)
  - Added citation extraction for audit trail support

**Other Updates**
- Added audit trail system for source attribution
- Created `output/` directory for saved research results
- Added Phase 4 to workflow for automatic markdown export

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/az9713) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
