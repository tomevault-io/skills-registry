---
name: yuans-writing-style
description: Yuan Tian's authentic voice and technical approach, PLUS Medium traffic optimization strategies. Use as foundation but experiment with new patterns for maximum engagement. Use when this capability is needed.
metadata:
  author: naity
---

# Yuan's Writing Style Guide

**Purpose**: Maintain Yuan's authentic voice while experimenting with traffic-optimized patterns.

**Philosophy**: Keep what makes Yuan's writing unique (technical depth, educational approach, credibility), but TEST new engagement strategies based on Medium best practices.

Based on analysis of Yuan Tian's published Medium articles (Resume Matcher, UFC Agent, Protein Transformers) + Medium traffic optimization feedback.

## Voice & Tone

### Core Characteristics
- **Technical but accessible**: PhD-level depth explained clearly for practitioners
- **Educational/tutorial focus**: "I'll walk through...", "Let's examine...", "We'll build..."
- **First-person when appropriate**: "I've realized", "I'll provide", "we can now"
- **Builds on foundations**: References previous work, establishes context before diving deep
- **Practical and applied**: Theory serves implementation, not vice versa

### Sentence Structure
- Mix of medium and longer sentences for technical explanations
- Short, punchy sentences for emphasis: "This creates a responsive experience."
- Uses parenthetical asides for clarifications: "(e.g., when users ask for...)"
- Strategic use of questions to engage: "Why this approach?"

## Opening Patterns

### Title Strategy: TEST Different Hooks

**What Yuan has used** (baseline):
- "Building X from Scratch"
- "Building an Agentic X: Python Foundations for Y"

**EXPERIMENT with traffic-optimized formats** (from Medium feedback):
1. **"How I Built..." Hook**
   - "How I Built a Meeting Coach That Actually Improves Your 1:1s"
   - "How I Built [Specific Result] with [Tech Stack]"
   - Promise: Specific, achievable outcome

2. **"Why..." Opinion Hook**
   - "Why Audio Transcription is the Missing Piece in Professional Development"
   - "Why [Controversial/Surprising Take]"
   - Promise: New perspective, contrarian insight

3. **Insider Knowledge Hook**
   - "7 Patterns I've Noticed as an AI Engineer at Amazon"
   - "What [Credential/Experience] Taught Me About [Topic]"
   - Promise: Exclusive insight from credibility

4. **Problem-Solution Hook**
   - "Meetings Feel Productive But Nothing Changes. Here's How AI Fixes That."
   - "[Pain Point]. [Specific Solution]."
   - Promise: Clear solution to relatable problem

**Choose based on project**:
- Technical deep-dive → "How I Built..." or traditional format
- Interesting design decision → "Why..." opinion
- Lessons learned → Insider knowledge
- Novel solution → Problem-solution

### Subtitle Structure
- **Current pattern**: Descriptive learning value (good foundation)
- **IMPROVE with**: Specific promise or result
  - BAD: "A guide to building transformers"
  - GOOD: "Build a production-ready antibody classifier in 500 lines of Python"
  - BETTER: "A step-by-step guide to building production-ready AI agents using async workflows and Pydantic"

### Introduction Pattern
**First Paragraph Structure:**
1. Start with context or problem statement
2. Reference previous work if building on it
3. State what this article will cover
4. Promise specific, actionable value

**Example opening**: "For someone who just got started building with Large Language Models (LLMs), it might seem a bit overwhelming... But after building numerous Generative AI (GenAI) applications, I've realized that some patterns are commonly used across different projects..."

**Key Technique**: Drop cap on first letter (uses `<span class="graf-dropCap">`) for visual impact

## Content Structure

### Required Sections (In Order)
1. **Introduction** (2-4 paragraphs)
   - Problem/Context
   - Solution overview
   - What reader will learn
   - Links to related work

2. **Architecture/Overview** (Early, with diagram)
   - High-level system design
   - **Always include**: Architecture diagram with caption "Figure X. [Description]. Source: Image by the author."
   - Explain components before diving into code

3. **Foundation/Component Sections**
   - Title pattern: "Foundation N: [Specific Concept]"
   - Or: "[Component Name]" as H3
   - Each section: Concept → Implementation → Code example

4. **Implementation Details**
   - Step-by-step code walkthroughs
   - Explain design decisions
   - Include "Why this approach?" subsections

5. **Results/Evaluation** (If applicable)
   - Show performance metrics
   - Include visualizations
   - Interpret results for reader

6. **Summary/Conclusion**
   - Recap what was covered
   - Emphasize practical value
   - Often includes "Future Work" subsection

7. **Acknowledgments**
   - Credits to data sources
   - Links to resources used
   - Community acknowledgment

8. **Call to Action**
   - GitHub repository link
   - LinkedIn connection invitation
   - Simple, friendly: "I am also happy to connect on [LinkedIn](url)."

### Section Length Guidelines
- Introduction: 200-400 words
- Architecture: 300-400 words + diagram
- Each foundation/component: 300-600 words
- Code-heavy sections: Prioritize explanation over code volume
- Conclusion: 150-300 words

## Technical Writing Patterns

### Code Presentation

**Code Block Format:**
```python
# Always include brief comments explaining key steps
def function_name(params):
    """
    Clear docstring explaining what the function does.
    """
    # Inline comments for complex logic
    result = implementation()
    return result
```

**Code Explanation Pattern:**
1. **Before code**: "Here's how we implement this:"
2. **Code block**: Syntax-highlighted, well-commented
3. **After code**: "This code performs X by doing Y..."
4. **Highlight key insights**: "The key insight here is..."

### Explaining Complex Concepts

**Pattern for Technical Depth:**
1. **High-level explanation**: What it does and why
2. **Visual aid**: Diagram or illustration if possible
3. **Technical detail**: How it works
4. **Code implementation**: Concrete example
5. **Key takeaways**: What to remember

**Example Structure** (from Resume Matcher article):
- "Embeddings are a fundamental concept..."
- Explains what they are
- Shows visual (Figure 3)
- Explains how they're used
- Provides code
- Summarizes: "This gives the model the ability to..."

### Design Decision Documentation
When explaining "why":
- "Why this approach?" as subsection
- List benefits with ✅ emoji
- Compare to alternatives when relevant
- Be specific: "This separation is advantageous because..."

## Medium Traffic Optimization (EXPERIMENT AGGRESSIVELY)

### Key Insights from Medium Feedback

**The Problem**: Great technical content, but low traffic due to packaging.

**The Solution**: Keep technical depth, improve discovery and engagement.

### Priority 1: Title & Hook Testing

**Always generate 3 title variations**:
1. **How-To**: "How I Built [Specific Result] with [Tech/Pattern]"
2. **Opinion**: "Why [Contrarian Take] About [Topic]"
3. **Insider**: "[N] Patterns I've Noticed as [Credential] at [Company]"

**Test which resonates**. The technical content stays the same, but the entry point changes.

### Priority 2: Credibility Front-Loading

**Current**: Credibility sometimes buried deep in post  
**Improve**: Establish authority in first 2-3 paragraphs

**Patterns to try**:
- "As an AI engineer at Amazon with a PhD, I've built several GenAI production systems. Here's the architecture pattern I keep coming back to..."
- "After building [N] production [systems/agents/models] at Amazon, I've noticed [pattern]..."

**Don't be humble**. Medium readers want to learn from someone with authority.

### Priority 3: Publication Targeting

**Instead of**: Publishing on own profile  
**Target**: Towards Data Science, Better Programming, Towards AI

**Why**: These publications have built-in audiences. Same content, 10x reach.

**How**: Write draft first, then submit unpublished to publication.

### Priority 4: Engagement Mechanics

**Subheadings must promise value**:
- BAD: "Implementation"
- GOOD: "Building the Authentication System"
- BETTER: "How I Built Authentication That Scales to 10K Users"

**First paragraph must hook**:
- Start with relatable problem OR surprising insight
- Establish credibility within first 100 words
- Promise specific, achievable outcome

**Use "power words"** in moderation:
- Actual, Specific, Proven, Complete, Production-ready
- Avoid: Revolutionary, Game-changing (too hype)

## Engagement & Medium Optimization

### Credibility Signals
**Natural Integration BUT Front-Loaded:**
- In introduction (first 2-3 paragraphs) when relevant
- "As an AI engineer at Amazon with a PhD..."
- "After building numerous Generative AI applications..."
- Don't be modest - readers want to learn from someone with authority

### Visual Elements
**Figures:**
- Number sequentially (Figure 1, Figure 2...)
- Descriptive captions
- Always attribute: "Source: Image by the author."
- Place figures immediately after relevant text
- Use GIFs for workflows/animations when helpful

**Lists and Structure:**
- Bullet points for quick scanning
- Numbered lists for sequential steps
- Use bold for emphasis on key terms
- Strategic use of block quotes for important notes

### Subheading Strategy
**Effective Patterns:**
- Descriptive, not generic: "Handling Tool Requests and Results" not "Implementation"
- Promise specific value: "Generating the Final Recommendation"
- Use numbers when sequential: "Foundation 1: Embeddings and Vector Databases"
- Ask questions occasionally: "Why Lopes Could Pull the Upset"

### Code-to-Explanation Ratio
- Aim for 40% code, 60% explanation
- Never dump code without context
- Always explain "why" before "how"
- Summarize key points after complex code blocks

## Medium-Specific Techniques

### Traffic Optimization
**Title Testing (Present Options):**
- How-To Hook: "How I Built [Specific Result]"
- Opinion Hook: "Why [Controversial/Surprising Take]"
- Technical Deep-Dive: "Building [X]: [Specific Framework/Pattern]"

**SEO Keywords (Natural Integration):**
- Include in title: Python, GenAI, LLM, Transformer, etc.
- Repeat 3-5 times naturally in body
- Use in first and last paragraphs
- Don't keyword stuff

### Internal Linking
- Link to previous articles when building on them
- Format: "Building on our understanding of X from the **[previous article](url)**"
- Use anchor text that describes destination
- Bold important links

### External References
- Link to papers: "introduced in the groundbreaking paper '[Title](url)'"
- Credit tools/frameworks: "[LangChain](url) and [LlamaIndex](url)"
- Acknowledge inspiration: "inspired by the blog post '[Title](url)' by [Author]"

## What Makes Yuan's Style Unique (PRESERVE THESE)

### Core Voice Elements - Keep Authentic
1. **Technical depth + accessibility**: PhD-level concepts, clearly explained
2. **Progressive complexity**: Each section builds naturally on previous
3. **Code as teaching tool**: Not just showing, but explaining deeply
4. **Complete working examples**: Full GitHub repos, reproducible
5. **Balanced tone**: Neither too casual nor too academic
6. **Educational focus**: "Let's build", "Here's how", "We'll explore"

### Patterns Yuan Has Used (OPTIONAL - Don't Force)
1. **"Foundation N:" naming** - Good for teaching fundamentals, but try other patterns too
2. **"Building from Scratch"** titles - Works, but test "How I..." and "Why..." too
3. **Architecture diagram early** - Keep this! But experiment with placement
4. **Long-form comprehensive** - Good, but also try focused deep-dives on one pattern

### Phrases Yuan Uses Often
- "Let's take a look at..."
- "Here's how we can implement..."
- "The key insight here is..."
- "This is necessary because..."
- "Let's examine..."
- "Now that we've covered X, let's explore Y..."
- "Upon completion..."
- "With this setup..."

### Phrases to Avoid
- Overly casual: "Let's dive in!", "Awesome!", "Cool!"
- Marketing speak: "revolutionary", "game-changing", "disrupting"
- Vague promises: "You'll be amazed", "This will blow your mind"
- Unnecessary hype: Avoid exclamation marks unless truly warranted

## Markdown & Formatting

### Code Blocks
```python
# Use triple backticks with language
# Include file path in context if relevant
# Keep blocks focused (10-30 lines ideal)
# Omit boilerplate with "# ... (other parts)"
```

### Emphasis
- **Bold** for key terms on first use
- *Italic* sparingly (Yuan rarely uses)
- `inline code` for functions, variables, filenames
- > Block quotes for important notes (use sparingly)

### Lists
- Use bullet points for non-sequential items
- Numbered lists for steps
- Sub-bullets for hierarchy
- Keep items parallel in structure

## Project-Specific Adaptations

### For Different Project Types

**API/Backend Projects:**
- Emphasize architecture early
- Show request/response flows
- Include FastAPI/framework patterns
- Highlight async patterns

**ML/AI Projects:**
- Start with problem/data
- Show architecture before code
- Include evaluation metrics
- Visualize results

**Agent/Tool Projects:**
- Explain agent loop/workflow
- Show tool definitions clearly
- Demonstrate reasoning process
- Include workflow diagrams

## Final Checklist

Before publishing, ensure:
- [ ] Title follows proven pattern
- [ ] Subtitle promises specific value
- [ ] Introduction includes credibility signal
- [ ] Architecture diagram included early
- [ ] Code blocks have explanations before AND after
- [ ] Figures numbered and attributed
- [ ] Technical depth balanced with accessibility
- [ ] GitHub link included
- [ ] LinkedIn connection invite
- [ ] Acknowledgments section present
- [ ] All links functional
- [ ] Subheadings descriptive and scannable

## Target Publications

Yuan's articles would fit well in:
- **Towards Data Science** - ML/AI technical deep-dives
- **Better Programming** - Python implementation focus
- **Towards AI** - GenAI and agent content

---

## Usage Notes for Agents

When writing blog posts in Yuan's style:

### PRESERVE (Yuan's authentic voice)
1. **Technical depth + accessibility** - PhD-level concepts explained clearly
2. **Explain decisions** - Never just show code, explain why
3. **Build progressively** - Each section builds on previous
4. **Be thorough but focused** - Depth without verbosity
5. **Include visuals** - Diagrams, charts, code structure
6. **Provide complete examples** - GitHub repos, full implementations
7. **End with resources** - Links, acknowledgments, connections

### EXPERIMENT (Traffic optimization)
1. **Title formats** - Test "How I...", "Why...", "N Patterns..." - don't default to "Building X"
2. **Opening hooks** - Try problem-first, opinion-first, result-first - not always context-first
3. **Credibility placement** - Front-load in first 2-3 paragraphs, not buried
4. **Subheading formats** - Promise specific outcomes, not just describe sections
5. **Structure variations** - Try focused deep-dives, not always comprehensive guides
6. **Publication targeting** - Write with TDS/Better Programming in mind

### BALANCE
- Keep Yuan's educational, thorough approach
- But package it for maximum discovery and clicks
- Technical excellence + traffic optimization = success

**The goal**: Write posts that are UNMISTAKABLY Yuan's voice, but with 3-5x more traffic through better packaging.

This is technical blogging that teaches AND attracts readers.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/naity) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
