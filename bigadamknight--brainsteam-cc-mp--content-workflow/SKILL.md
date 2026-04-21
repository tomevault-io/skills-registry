---
name: content-workflow
description: Complete guide to the thought leadership content creation pipeline. Shows how to use voice-profile, trendmaster, content-interview, thought-leader-content, social-publisher, and content-analytics together. Use when this capability is needed.
metadata:
  author: bigadamknight
---

# Content Workflow Guide

End-to-end pipeline for creating thought leadership content in your authentic voice.

## The Pipeline

```
LEARN (once)       RESEARCH        EXTRACT         CREATE          PUBLISH         MEASURE
voice-profile  ->  trendmaster ->  interview  ->  templates  ->  publisher  ->  analytics
     |                  |              |              |              |              |
  Store in          Find what      Get your       Draft in      Post to         Track
  memory           to write        insights       your voice    platforms       results
                   about
```

## Phase 1: Learn Your Voice (One-Time Setup)

Before creating content, teach the system your voice:

```
/learn-voice "paste transcript or article here" --type transcript
```

Or analyze existing social media:
```
/learn-voice "LinkedIn posts content" --type social
```

The voice-analyzer will extract patterns and store them in memory. View what it learned:
```
/voice-status
```

## Phase 2: Research What to Write About

Use trendmaster to find emerging topics in your industry:

```
/find-trends "AI agents for business"
```

Get content ideas based on trends:
```
/trend-content-ideas
```

The trend-researcher will:
- Search current conversations and news
- Identify emerging topics
- Rate content potential
- Suggest angles

## Phase 3: Extract Your Unique Insights

Start an AI interview to extract your perspective on a topic:

```
/interview "How AI agents will change small business operations"
```

The interviewer agent will:
- Ask probing questions
- Draw out stories and frameworks
- Capture contrarian views
- Save transcript to working memory

Convert interview to structured content:
```
/interview-to-article
```

## Phase 4: Create Content

Use thought-leader-content templates based on platform:

**LinkedIn Post:**
- Story-Lesson format
- Contrarian Take
- Framework Introduction

**Twitter Thread:**
- Listicle thread
- How-To breakdown
- Story thread

**Long-form Article:**
- Problem-Solution structure
- Framework explanation
- Case study format

The voice-adapted-editor will automatically load your voice profile from memory to ensure the content sounds like you.

## Phase 5: Publish

Review and publish to platforms:

```
/publish-linkedin
/publish-twitter
```

Or schedule for later:
```
/schedule-post linkedin "2024-01-15 09:00"
```

**All publishing requires explicit confirmation** - you'll see exactly what will be posted before it goes live.

## Phase 6: Measure & Optimize

Check what's working:
```
/what-worked
```

Deep dive on specific platform:
```
/content-performance linkedin --period month
```

## Quick Reference

| Task | Command |
|------|---------|
| Learn voice | `/learn-voice` |
| Check voice | `/voice-status` |
| Find trends | `/find-trends` |
| Get ideas | `/trend-content-ideas` |
| Start interview | `/interview` |
| Convert to article | `/interview-to-article` |
| Publish LinkedIn | `/publish-linkedin` |
| Publish Twitter | `/publish-twitter` |
| Schedule post | `/schedule-post` |
| Check performance | `/what-worked` |
| Deep analysis | `/content-performance` |

## Tool Integration

### Memory Flow

```
voice-profile   -->  semantic memory  -->  voice-adapted-editor
(learns voice)       (stores patterns)     (loads & applies)

trendmaster     -->  working memory   -->  thought-leader-content
(finds topics)       (current research)    (uses for ideas)

social-publisher -->  episodic memory -->  content-analytics
(records publish)     (tracks posts)       (analyzes results)
```

### Complementary Tools

| New Plugin | Works With | How |
|------------|-----------|-----|
| voice-profile | voice-adapted-editor | Profile learns, editor applies |
| trendmaster | deep-research | Trends for quick scans, deep-research for comprehensive |
| thought-leader-content | content-research-writer | Templates for quick posts, research-writer for deep articles |

## Recommended Workflows

### Weekly Content Creation

1. Monday: `/find-trends` - Research week's topics
2. Tuesday: `/interview` - Extract insights on top topic
3. Wednesday: Draft 2-3 posts using templates
4. Thursday: Review and schedule
5. Friday: `/what-worked` - Check last week's performance

### Quick Post Creation

1. `/trend-content-ideas` - Get quick ideas
2. Pick a topic, write draft
3. Run through voice-adapted-editor to match your voice
4. `/publish-linkedin` - Publish

### Deep Thought Leadership

1. `/interview` - 20-minute deep dive on topic
2. `/interview-to-article` - Convert to long-form
3. Edit and refine
4. Repurpose into social posts

## Requirements

- **AGI Memory**: All plugins use memory for storage/retrieval
- **Rube MCP**: Required for social-publisher (LinkedIn/Twitter APIs)
- **Web Search**: Required for trendmaster research

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/bigadamknight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
