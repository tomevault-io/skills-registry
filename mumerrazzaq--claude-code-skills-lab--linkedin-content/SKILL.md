---
name: linkedin-content
description: Create LinkedIn posts, bios, comments, and articles that sound authentically human.  This skill should be used when the user wants to write LinkedIn content, says "write a post", "update my bio", "comment on this", or "draft an article". Use when this capability is needed.
metadata:
  author: mumerrazzaq
---

# LinkedIn Content Skill

Generate LinkedIn content that sounds human, builds credibility, and drives engagement.

## The Problem We're Solving

54% of LinkedIn long-form posts are now AI-generated. AI content gets 45% less engagement because readers detect it instinctively. This skill creates content that sounds like YOU, not like ChatGPT.

## Key Stats (2025-2026)

| Metric                 | Value                               |
| ---------------------- | ----------------------------------- |
| AI content penalty     | 30% less reach, 55% less engagement |
| Optimal post frequency | 3-4 times per week                  |
| Golden hour            | First 60-90 minutes critical        |
| Hashtag limit          | Max 5 (more = 35% reach drop)       |
| Comments importance    | #1 algorithm factor                 |
| Link penalty           | Up to 70% if in main post           |

## Workflow

```
User describes topic → Identify content type → Apply anti-AI rules → Generate + shorter version
```

## Content Types

| Type             | Trigger                 | Length         |
| ---------------- | ----------------------- | -------------- |
| **Post**         | "write a post about..." | 50-150 words   |
| **Bio/Headline** | "update my bio"         | 1-4 lines      |
| **Comment**      | "comment on this"       | 2-4 sentences  |
| **Article**      | "write an article"      | 800-1500 words |

## Output Format

Always provide TWO versions:

```markdown
## LinkedIn Post

[Primary version - ready to post]

---

## Shorter Version

[Condensed alternative]

---

## Hashtags (add at bottom)

#tag1 #tag2 #tag3

## Notes

- [Link placement suggestion]
- [Best time to post]
```

---

## Anti-AI-Slop Rules (Critical)

See `references/anti-ai-patterns.md` for full list.

### Words to NEVER Use

These scream "AI-generated" - avoid completely:

```
delve, tapestry, realm, embark, unlock, unleash, harness,
leverage, cutting-edge, robust, vital, crucial, paramount,
in today's digital age, it's important to note, game changer,
landscape, testament, moreover, furthermore, additionally,
in the realm of, navigate, elevate, revolutionize, foster
```

### Patterns to Avoid

| AI Pattern                 | Human Alternative          |
| -------------------------- | -------------------------- |
| Perfect grammar throughout | Natural imperfections okay |
| Uniform sentence length    | Vary short and long        |
| "I'm excited to announce"  | Direct statement           |
| Generic statements         | Specific examples          |
| Repetitive structure       | Varied formatting          |
| Overly polished            | Conversational             |

### How to Sound Human

1. **Add personal story** - Specific example from YOUR experience
2. **Use contractions** - "I'm", "don't", "can't" (AI often doesn't)
3. **Include imperfection** - One casual phrase or incomplete thought
4. **Be specific** - Numbers, names, dates (not vague claims)
5. **Read aloud test** - If you wouldn't say it, rewrite it

---

## Post Structure

### Hook (First 150 characters)

Critical - this shows before "See more". Must stop the scroll.

**Good hooks:**

- Question that creates curiosity
- Surprising stat or insight
- Bold statement/hot take
- Personal failure or lesson

**Bad hooks:**

- "I'm excited to share..."
- "In today's fast-paced world..."
- Generic announcement

### Body

- **Short paragraphs** (1-2 sentences max)
- **Bullet points** for features/lists
- **One idea per paragraph**
- **White space** between sections

### CTA (End)

- Ask a question
- Invite comments
- Share link in FIRST COMMENT (not in post)

---

## Platform Rules (2025-2026 Algorithm)

See `references/algorithm-insights.md`. Quick reference:

| Do                       | Don't                       |
| ------------------------ | --------------------------- |
| Post 3-4x per week       | Post multiple times per day |
| Engage in first 60 mins  | Post and disappear          |
| Put links in comments    | Put links in post body      |
| Use 3-5 hashtags         | Use 9+ hashtags             |
| Native video (under 90s) | YouTube/external links      |
| Ask questions            | Engagement bait             |
| Share real experiences   | Generic advice              |

---

## Content Type Templates

### Project/Accomplishment Post

```
[What you built - direct statement, no "excited to announce"]

[1-2 sentences on what it does/solves]

What I learned building this:
- [Specific insight 1]
- [Specific insight 2]

[Link in first comment]

Would love to hear your thoughts.
```

### Learning Milestone

```
[What you achieved - specific]

[Why it matters to you personally]

The biggest takeaway: [One specific insight]

Next: [What you're doing with this]
```

### Tech Explainer

```
[Question or bold statement about the tech]

[Simple explanation - like explaining to a friend]

Why this matters: [Practical application]

Have you tried this? What's your experience?
```

---

## Bio/Headline Rules

**Headline format:** `[Role] | [Specialty] | [Differentiator]`

Example for user:

```
AI Automation Engineer | n8n & Python Workflows | Building Agentic AI Systems
```

**Bio structure:**

- Line 1: What you do (specific)
- Line 2: Who you help / What you build
- Line 3: Key tools/skills
- Line 4: Credibility marker

---

## Comment Rules

- Add genuine insight (not "Great post!")
- 2-4 sentences max
- Connect to YOUR experience
- No emojis in comments
- Ask a follow-up question

**Template:**

```
[Agree/expand on specific point they made]
[Add your perspective or related experience]
[Optional: thoughtful question]
```

---

## User Profile

**Name:** Muhammad Umer Razzaq
**Niche:** AI Automation & n8n Workflows
**Certified:** PIAIC Agentic AI Engineer

**Topics to post about:**

- AI projects built
- Automation workflows
- Learning milestones (courses, certs)
- Tech explainers (n8n, Agentic AI, CrewAI)
- SDD/TDD methodology

**Voice characteristics:**

- Professional but warm
- Concise (always asks for shorter)
- Honest about learning journey
- Solution-oriented

---

## Checklist Before Output

- [ ] No AI-slop words (delve, realm, leverage, etc.)
- [ ] Hook in first 150 chars
- [ ] Under 150 words for posts
- [ ] Short paragraphs (1-2 sentences)
- [ ] Specific examples, not generic claims
- [ ] Read-aloud test passes
- [ ] 3-5 hashtags max
- [ ] Link goes in first comment
- [ ] Shorter version provided
- [ ] Sounds like the user, not ChatGPT

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mumerrazzaq) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
