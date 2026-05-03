---
name: seo-blog-post
description: Create SEO-optimized blog posts for My Job Trackr focused on job seeking, job searching, and job tracking topics. Generates shorter-form content (800-1200 words) with casual/friendly tone similar to social media. Includes keyword research and suggestions, meta titles/descriptions, header structure, internal/external linking recommendations, image alt text, and schema markup (Article and HowTo). Naturally integrates My Job Trackr features and links. Supports multiple structures including how-to guides, listicles, tips/advice articles, and tutorials. This skill must be used within the "My Job Trackr" project where brand voice and product information are available. Use when this capability is needed.
metadata:
  author: marcusr21
---

# SEO-Optimized Blog Post Generator

Create engaging, SEO-friendly blog posts for My Job Trackr that rank well and naturally showcase product features.

## Prerequisites

**CRITICAL:** This skill requires access to the "My Job Trackr" project context, which contains:
- Brand voice and tone guidelines
- Product features and capabilities
- Target audience insights
- Pricing and positioning information

If not currently in the "My Job Trackr" project, inform the user that this skill requires that project context.

## Before Starting

1. **Understand the Topic:** Clarify what the blog post should cover
2. **Review Project Context:** Check for relevant product features to mention
3. **Identify Post Type:** Determine structure (how-to, listicle, tips, tutorial)
4. **Consider SEO Goals:** Primary keywords and search intent

## Core SEO Principles

### Primary Keyword Focus
**Always target job-related keywords with "job tracking" integration:**
- Primary: Job seeking, job searching, job hunt, job application
- Secondary: Job tracking, application tracking, job organization
- Long-tail: How to track job applications, best way to organize job search, etc.

### Keyword Strategy
- **Primary keyword:** 1-2% density, appears in title, first paragraph, H2, conclusion
- **Secondary keywords:** Natural placement throughout
- **LSI keywords:** Related terms (career, resume, interview, hiring, etc.)
- **Avoid keyword stuffing:** Write naturally for humans first

## Content Structure

### Standard Blog Post Format

```
# [SEO-Optimized Title with Primary Keyword]

[Engaging introduction paragraph with primary keyword]
[Hook the reader with a relatable problem or question]
[Preview what they'll learn]

## [H2: Main Section 1]

[Content with natural keyword integration]
[My Job Trackr feature mention where relevant]

## [H2: Main Section 2]

[Content continues]
[Tips, advice, or steps]

## [H2: Main Section 3]

[More valuable content]
[Additional feature mentions]

## Conclusion

[Summarize key points]
[CTA mentioning My Job Trackr]
[Primary keyword in final paragraph]
```

### Post Types & Structures

**1. How-To Guides**
- Title: "How to [Do Something] in [Time Period/Context]"
- Structure: Introduction → Step 1-5 → Tips → Conclusion
- Include: Numbered steps, practical advice, tool mentions

**2. Listicles**
- Title: "X [Number] Ways to [Achieve Goal]"
- Structure: Introduction → List items as H2s → Conclusion
- Include: 5-10 items, brief explanations, examples

**3. Tips & Advice**
- Title: "X Tips for [Job Search Challenge]"
- Structure: Introduction → Tip sections → Conclusion
- Include: Actionable advice, real-world examples, best practices

**4. Tutorials**
- Title: "The Complete Guide to [Job Search Topic]"
- Structure: Introduction → Background → Process → Advanced tips → Conclusion
- Include: Detailed explanations, screenshots (suggest), tool usage

## Writing Guidelines

### Tone & Style
- **Casual and friendly** (like social media content)
- **Approachable and supportive** (job searching is stressful)
- **Conversational** but informative
- **Encouraging** without being cheesy
- **Authentic** - avoid sounding generic or AI-written

### Content Requirements
- **Length:** 800-1200 words (shorter, digestible posts)
- **Paragraphs:** 2-4 sentences max (scannable)
- **Subheadings:** Every 200-300 words (H2, H3 structure)
- **No double dashes (--)** - consistent with brand guidelines
- **Natural product mentions:** 2-3 times per post, contextually relevant

### My Job Trackr Integration

**Natural Integration Examples:**

✅ GOOD:
- "Using a tool like My Job Trackr helps you stay organized by tracking all your applications in one place."
- "Set deadline reminders so you never miss a follow-up (My Job Trackr's reminder feature makes this easy)."
- "Keep all job descriptions saved with each application - that's what we built My Job Trackr for."

❌ AVOID:
- "My Job Trackr is the best app ever and you should download it now!"
- Forcing product mentions where they don't fit naturally
- Multiple product pitches in one paragraph

**Link Placement:**
- First mention: Link to homepage or relevant feature page
- Subsequent mentions: Can mention without linking or link to different features
- Natural anchor text: "job tracking app" not "click here"

## Technical SEO Requirements

### Meta Information

**Meta Title (50-60 characters):**
- Include primary keyword
- Include brand name "| My Job Trackr" at end
- Stay under 60 characters total
- Make it compelling and clickable

Example: "How to Track Job Applications Effectively | My Job Trackr"

**Meta Description (150-160 characters):**
- Include primary keyword
- Summarize value proposition
- Include CTA or benefit
- Stay under 160 characters

Example: "Learn how to track your job applications and never miss a deadline. Discover organization tips and tools to streamline your job search."

### Header Hierarchy

**H1 (Title):**
- Only one H1 per post
- Include primary keyword
- Make it engaging and specific
- 60 characters or less for best SEO

**H2 (Main Sections):**
- 3-5 H2s per post
- Include secondary keywords naturally
- Descriptive and scannable
- Parallel structure when possible

**H3 (Subsections):**
- Use sparingly for breaking down H2 content
- Natural language, don't force keywords

### Internal Linking Strategy

**Link to:**
- Other My Job Trackr blog posts (suggest relevant topics)
- Feature pages on My Job Trackr website
- Product pages (homepage, pricing, features)
- Help guides and tutorials

**Best Practices:**
- 2-4 internal links per post
- Use natural anchor text with keywords
- Link to relevant, helpful content
- Suggest specific pages to link to

### External Linking Strategy

**Link to:**
- Authoritative sources (industry reports, studies)
- Reputable career sites (LinkedIn, Indeed, Glassdoor)
- Government job resources
- News sources for statistics

**Best Practices:**
- 1-3 external links per post
- Only link to high-quality, authoritative sites
- Open in new tab
- Add rel="nofollow" for less authoritative sources

### Image Recommendations

**Suggest Images:**
- Header image (featured image for post)
- Supporting images for each major section
- Screenshots of My Job Trackr features when mentioned
- Infographics or charts for data/tips

**Alt Text Format:**
- Descriptive with primary or secondary keyword
- 10-15 words
- Describe what's in the image
- Example: "Job seeker tracking multiple applications in My Job Trackr dashboard"

### Schema Markup

**Article Schema (Required for all posts):**
```json
{
  "@context": "https://schema.org",
  "@type": "Article",
  "headline": "[Post Title]",
  "description": "[Meta Description]",
  "author": {
    "@type": "Organization",
    "name": "My Job Trackr"
  },
  "publisher": {
    "@type": "Organization",
    "name": "My Job Trackr",
    "logo": {
      "@type": "ImageObject",
      "url": "[My Job Trackr Logo URL]"
    }
  },
  "datePublished": "[YYYY-MM-DD]",
  "dateModified": "[YYYY-MM-DD]",
  "image": "[Featured Image URL]",
  "mainEntityOfPage": "[Post URL]"
}
```

**HowTo Schema (For how-to guides and tutorials):**
```json
{
  "@context": "https://schema.org",
  "@type": "HowTo",
  "name": "[Post Title]",
  "description": "[Brief description of what user will learn]",
  "totalTime": "PT[X]M",
  "step": [
    {
      "@type": "HowToStep",
      "name": "[Step Title]",
      "text": "[Step instructions]",
      "position": 1
    },
    {
      "@type": "HowToStep",
      "name": "[Step Title]",
      "text": "[Step instructions]",
      "position": 2
    }
  ]
}
```

## SEO Optimization Checklist

Before delivering blog post, verify:

**Content Quality:**
- [ ] 800-1200 words
- [ ] Primary keyword in title, intro, conclusion
- [ ] 1-2% keyword density (natural, not forced)
- [ ] Casual, friendly tone consistent with brand
- [ ] No double dashes (--)
- [ ] 2-3 natural My Job Trackr mentions with links

**Technical SEO:**
- [ ] Meta title (50-60 chars, includes keyword + brand)
- [ ] Meta description (150-160 chars, compelling)
- [ ] Proper header hierarchy (H1 → H2 → H3)
- [ ] 2-4 internal links with natural anchor text
- [ ] 1-3 external links to authoritative sources
- [ ] Image suggestions with SEO-optimized alt text
- [ ] Article schema markup provided
- [ ] HowTo schema (if applicable) provided

**Readability:**
- [ ] Short paragraphs (2-4 sentences)
- [ ] Subheadings every 200-300 words
- [ ] Scannable format
- [ ] Clear, actionable advice
- [ ] Conclusion with CTA

## Keyword Research & Suggestions

When generating a blog post, include a **Keyword Strategy** section:

**Primary Keyword:** [main keyword, search volume estimate]
**Secondary Keywords:** [2-4 related keywords]
**LSI Keywords:** [semantically related terms to use naturally]
**Long-tail Opportunities:** [specific phrases to target]

**Competitor Analysis:** [Brief note on what competitors are ranking for]
**Content Gap:** [What's missing from existing content you can address]

## Output Format

Deliver blog posts in this structure:

```markdown
# [SEO Title]

---

## SEO Metadata

**Meta Title:** [50-60 characters]
**Meta Description:** [150-160 characters]
**Primary Keyword:** [keyword]
**Focus Keyphrase:** [main phrase]

---

## Keyword Strategy

**Primary:** [keyword + volume]
**Secondary:** [keywords]
**LSI:** [related terms]
**Long-tail:** [opportunities]

---

[Blog Post Content]

---

## Internal Linking Opportunities

1. Link to: [suggested page/post] - Anchor text: "[suggested text]"
2. Link to: [suggested page/post] - Anchor text: "[suggested text]"

---

## External Link Recommendations

1. Link to: [authoritative source] for [reason]
2. Link to: [authoritative source] for [reason]

---

## Image Recommendations

1. **Header Image:** [Description]
   - Alt text: "[SEO-optimized alt text]"

2. **Section Image:** [Description]
   - Alt text: "[SEO-optimized alt text]"

---

## Schema Markup

### Article Schema
```json
[Article schema code]
```

### HowTo Schema (if applicable)
```json
[HowTo schema code]
```

---

## SEO Performance Notes

- **Target audience:** [description]
- **Search intent:** [informational/navigational/transactional]
- **Estimated reading time:** [X minutes]
- **Suggested publish date:** [Date/timing notes]
```

## Content Ideas & Topics

**Job Search Organization:**
- How to track job applications effectively
- Best practices for organizing your job search
- Job application tracking tips
- Creating a job search system

**Application Process:**
- How to follow up on job applications
- Tracking application deadlines
- Managing multiple job offers
- Keeping job descriptions organized

**Job Search Strategy:**
- X tips for a successful job search
- Job hunting mistakes to avoid
- How to stay motivated during job search
- Setting job search goals

**Tools & Productivity:**
- Tools every job seeker needs
- Simplifying your job search workflow
- Using technology to track applications
- Job search apps that actually help

**Career Advice:**
- Resume organization tips
- Interview preparation strategies
- Networking during job search
- Career change guidance

## Working with Project Context

When generating blog posts:
1. Reference My Job Trackr features from project knowledge
2. Use brand voice guidelines from project conversations
3. Check pricing and product positioning
4. Look for recent feature launches to mention
5. Maintain tone consistency with social media content

## Common Requests & Responses

**"Write a blog post about job tracking"**
→ Clarify: How-to guide, tips article, or tutorial?
→ Generate post with "job tracking" as primary keyword
→ Naturally integrate My Job Trackr throughout

**"Create an SEO post for [topic]"**
→ Research keywords related to topic
→ Suggest primary and secondary keywords
→ Write optimized post with full SEO metadata

**"Make this more SEO-friendly"**
→ Analyze current keyword usage
→ Add missing meta information
→ Improve header structure and internal links
→ Provide schema markup

**"I need a how-to guide about [topic]"**
→ Use numbered step structure
→ Include HowTo schema markup
→ Make it actionable and practical
→ Feature My Job Trackr as solution tool

## Quality Standards

**Content must be:**
- Genuinely helpful and valuable
- Well-researched and accurate
- Original and unique (not regurgitated content)
- Optimized for SEO without sacrificing readability
- Naturally promotional (not salesy)
- Aligned with My Job Trackr brand voice

**Avoid:**
- Keyword stuffing
- Thin or low-value content
- Generic advice without specifics
- Over-optimization
- Robotic or stiff writing
- Forcing product mentions

## Tips for Better SEO Content

**DO:**
- Write for humans first, search engines second
- Use keywords naturally in context
- Break up text with subheadings and lists
- Link to authoritative external sources
- Update content regularly
- Include recent statistics and data
- Answer user questions directly

**DON'T:**
- Stuff keywords unnaturally
- Write only for search engines
- Create thin, low-value content
- Ignore user intent
- Over-optimize to the point of poor readability
- Copy competitor content
- Forget mobile readers (keep it scannable)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/marcusr21) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
