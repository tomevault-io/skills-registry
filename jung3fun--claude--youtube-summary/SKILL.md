---
name: youtube-summary
description: This skill should be used when Claude needs to create structured, educational summaries of YouTube videos. Use this skill when the user requests a video summary, content analysis, or learning-focused breakdown of YouTube content. The skill integrates youtube-video-info and youtube-transcript skills to gather video data and transcripts, then generates comprehensive summaries following a specific educational format with key topics, insights, data-based analysis, and exploratory questions. Use when this capability is needed.
metadata:
  author: jung3fun
---

# YouTube Summary

## Overview

Create structured, educational summaries of YouTube videos following a comprehensive analysis format. This skill combines video metadata and transcript extraction to generate learning-focused summaries with key topics, insights, numerical analysis, and exploratory questions designed for educational use.

## When to Use This Skill

Use this skill when:
- User requests a summary or analysis of a YouTube video
- User wants to understand the main points of a video without watching
- User needs educational content breakdown for learning purposes
- User asks for key insights, data points, or critical analysis of video content
- User wants a structured breakdown with timestamps and direct links

## Summary Workflow

### Step 1: Gather Video Information

First, use the `youtube-video-info` skill to fetch video metadata:

```bash
python /Users/dante/.claude/skills/youtube-video-info/scripts/get_youtube_info.py "<youtube_url>"
```

Extract:
- `video_id` - For generating timestamp links
- `title` - Video title
- `duration` - Total playback time

### Step 2: Extract Video Transcript

Next, use the `youtube-transcript` skill to get the full transcript:

```bash
python /Users/dante/.claude/skills/youtube-transcript/scripts/get_youtube_transcript.py "<youtube_url>"
```

Extract:
- `transcript` - Full transcript with timestamps
- `formatted_transcript` - Human-readable version

### Step 3: Analyze and Summarize

Follow the summary guidelines from `references/summary_guide.md` to create a comprehensive educational summary.

**Key Analysis Components:**

1. **핵심 주제 (Core Topics)**
   - Identify the main themes and ideas
   - Summarize the central message

2. **핵심 내용 (Key Content)**
   - Extract important claims, concepts, examples, and cases
   - Highlight significant arguments and supporting evidence

3. **논리 전개 (Logical Flow)**
   - Trace how the video reaches its conclusions
   - Identify reasoning patterns and evidence used

4. **맥락 연결 (Context Connection)**
   - Link to related background, citations, or conceptual extensions
   - Connect to broader themes or disciplines

5. **데이터 기반 인사이트 (Data-Based Insights)**
   - Extract numerical data, statistics, percentages, years mentioned
   - Interpret what these numbers signify
   - Example: "60% of users → indicates a trend inflection point"

6. **탐구형 질문 (Exploratory Questions)**
   Generate 3 types of questions:
   - Questions that encourage critical thinking about core claims
   - Questions that extend the topic to other contexts or fields
   - Questions that explore practical applications or limitations

### Step 4: Format Output

Structure the summary according to the template in `references/summary_guide.md`:

```markdown
1. 제목: {title}

2. 전체 재생시간: {total_playback_time}

3. 전체요약
🎯 핵심 주제: ...
💡 핵심 내용
 - Point 1
 - Point 2
 - ...
🔍 논리 전개
 - ...
🧩 맥락 연결
 - ...
🔢 숫자 기반 인사이트
 - ...
🧠 탐구형 질문
 - E1: ...
 - E2: ...
 - E3: ...

4. 구간별 요약
 - 구간1: {interval_title} (⏱️{time_interval})
 {concise_summary}
 [바로가기] https://www.youtube.com/watch?v={video_id}&t={timestamp}s

 - 구간2: ...
 ...
```

## Formatting Guidelines

**Time Formatting:**
- Total playback time: `[mm:ss]` format
- Time intervals: `[mm:ss–mm:ss]` format
- Timestamp links: `&t={seconds}s` format (e.g., `&t=11s`)

**Direct Links:**
Generate clickable links to specific video timestamps:
```
https://www.youtube.com/watch?v={video_id}&t={timestamp_in_seconds}s
```

Use transcript timestamps to calculate exact second values for each section.

## Content Guidelines

**DO:**
- Interpret and restructure content from the transcript
- Extract and explain numerical data with context
- Generate insightful critical questions
- Connect concepts to broader themes
- Maintain educational focus

**DON'T:**
- Copy transcript text verbatim - always interpret and summarize
- Add information not present in the video
- Process unethical or copyright-restricted content
- Use generic summaries - provide specific, substantive analysis

## Example User Interactions

**Example 1: Basic video summary**
```
User: "Summarize this YouTube video: https://www.youtube.com/watch?v=dQw4w9WgXcQ"

Action:
1. Use youtube-video-info skill to get metadata
2. Use youtube-transcript skill to get full transcript
3. Read references/summary_guide.md for format
4. Analyze transcript content following the guidelines
5. Generate structured summary with all required sections
6. Format with proper timestamps and direct links
```

**Example 2: Educational analysis**
```
User: "I need an educational breakdown of this tech talk with key insights"

Action:
1. Fetch video info and transcript
2. Focus on data-based insights section
3. Extract all numerical data and statistics
4. Generate exploratory questions for deeper learning
5. Create section-by-section breakdown with timestamps
```

**Example 3: Quick overview**
```
User: "What are the main points of this video?"

Action:
1. Get transcript
2. Focus on core topics and key content sections
3. Provide concise summary highlighting main arguments
4. Include 2-3 most important exploratory questions
```

## Section Breakdown Strategy

When dividing content into sections:

1. **Identify Natural Breaks**
   - Topic changes in the transcript
   - Major argumentative shifts
   - Introduction, body, conclusion structures

2. **Create Descriptive Titles**
   - Each section should have a clear, informative title
   - Titles should reflect the core content of that segment

3. **Keep Sections Focused**
   - Aim for 3-6 main sections for typical videos
   - Each section should cover 30 seconds to 3 minutes of content
   - Balance granularity with readability

4. **Include Precise Timestamps**
   - Use exact timestamps from transcript data
   - Convert to seconds for direct links
   - Format as [mm:ss] for display

## Quality Standards

**Comprehensive Analysis:**
- Cover all major points from the video
- Include specific examples and cases mentioned
- Extract all quantitative data referenced

**Educational Value:**
- Generate questions that promote critical thinking
- Connect content to broader contexts
- Provide interpretation, not just description

**Accurate Attribution:**
- All information must come from the video transcript
- Clearly distinguish between video content and analytical interpretation
- Maintain factual accuracy in data representation

## Resources

### references/

**`summary_guide.md`** - Complete summary format guidelines
- Detailed output format template
- Section-by-section requirements
- Example summaries
- Quality guidelines
- Formatting specifications

Load this reference when creating summaries to ensure compliance with the educational format standards.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jung3fun) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
