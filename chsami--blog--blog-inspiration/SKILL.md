---
name: blog-inspiration
description: Search Reddit, X/Twitter, and Hacker News for trending AI & Programming topics to find blog post inspiration Use when this capability is needed.
metadata:
  author: chsami
---

# Blog Inspiration Finder

Search Reddit, X/Twitter, and Hacker News for the hottest AI & Programming discussions, then evaluate them as potential blog post topics using the journalism workflow from `docs/research.md` and the memory file `tech-journalism-workflow.md`.

## Step 1: Source Discovery

Run these searches in parallel to find trending discussions from the last 7 days. If the user provided $ARGUMENTS, use that as an additional filter on all searches.

### Hacker News
Search for: `site:news.ycombinator.com AI OR programming OR LLM OR "machine learning" OR "developer tools"` (last 7 days)
Also fetch `https://hn.algolia.com/api/v1/search?tags=front_page&query=AI+programming&hitsPerPage=15` to get current front page items with point counts.

### Reddit
Search for: `site:reddit.com (r/programming OR r/MachineLearning OR r/LocalLLaMA OR r/artificial OR r/ExperiencedDevs) AI OR LLM OR agents` (last 7 days)

### X / Twitter
Search for: `site:x.com AI OR LLM OR "developer tools" OR programming` (last 7 days)
Also search for: `site:nitter.net AI programming` as fallback.

## Step 2: Signal Scoring

For each discovered topic, evaluate it on these criteria (from Phase 1 of the journalism workflow):

1. **Discussion heat** — Are people actively debating, not just sharing? High comment counts + strong opinions = good signal.
2. **Cross-platform presence** — Does it appear on multiple platforms? Topics trending on both HN and Reddit are stronger candidates.
3. **Contrarian takes available** — Are there expert disagreements or nuanced perspectives? These become the angles that differentiate the blog post.
4. **User impact** — Does this affect how developers actually work day to day? Prioritize practical over theoretical.
5. **Freshness** — Is there a specific trigger (launch, paper, incident) or is it a slow-burn trend?

## Step 3: Check Against Existing Posts

Read the list of existing posts from `posts/` directory. Discard any topics already covered. Flag topics that could be interesting follow-ups to existing posts.

## Step 4: Present Results

Output a ranked list of the **top 5 topics** in this format:

```
## 1. [Topic Title]

**Signal strength:** 🔥🔥🔥🔥 (4/5)
**Sources:** [HN](link) | [Reddit](link) | [X](link)
**Discussion heat:** [one line on the debate]
**Blog angle:** [one line pitch using Phase 4 framework — lead with the user problem]
**Key community insight:** [the most interesting take from comments that wouldn't be in the primary source]
**Cross-platform:** Yes/No
```

After the list, suggest which topic you'd pick and why, referencing the Phase 4 angle questions:
- What problem does this solve?
- Why is this different from what exists?
- What does this mean for users?

## Step 5: Optional Deep Dive

Ask the user if they want to go deeper on any topic. If yes, run Phase 2 (Primary Source Research) and Phase 3 (Community Insight Gathering) from the journalism workflow:
- Fetch and read the original source
- Read the top comments from the HN/Reddit threads
- Extract expert perspectives, contrarian takes, and recurring themes
- Save findings to `docs/research.md` in the same format as previous research notes

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/chsami) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
