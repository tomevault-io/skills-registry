---
name: doc-contrib
description: Document a contribution for blogs, portfolios, or personal records. Use when user wants to write about their OSS contribution. Use when this capability is needed.
metadata:
  author: jay05410
---

# Contribution Documenter Skill

Document your open source contribution journey.

## When to Use

- User asks to document their contribution
- User wants to write a blog post about their contribution
- User mentions portfolio or resume
- User asks to summarize their contribution

## Process

1. **Collect contribution data via GitHub MCP**
   - Issue details and timeline
   - PR details and review history
   - Commits and code changes
   - Merge date and release info

2. **Build timeline**
   - Issue discovery
   - Analysis and planning
   - Implementation
   - PR submission
   - Review cycles
   - Merge

3. **Generate documentation**
   - Blog post format
   - Portfolio entry format
   - Detailed personal record

## Output Formats

### Blog Post Format

```markdown
# Contributing to [Project]: [Catchy Title]

## TL;DR
> [One-line summary]

## The Project
[Project description, stars, tech stack]

## Finding the Issue
[How you discovered it]

## Understanding the Problem
[Investigation process, root cause]

## The Solution
[Approaches considered, final implementation]

## The PR Journey
[Submission, review process, iterations]

## Lessons Learned
- Technical: [what you learned]
- Process: [OSS workflow insights]
- Communication: [working with maintainers]

## Links
- Issue: [URL]
- PR: [URL]
```

### Portfolio Entry Format

```markdown
## [Project] Contribution

| Aspect | Details |
|--------|---------|
| Project | [name] (⭐ [count]) |
| Period | [dates] |
| Issue | #[number] |
| PR | #[number] |
| Type | Bug Fix / Feature |
| Tech | [technologies] |

**Problem**: [one sentence]
**Solution**: [one sentence]
**Impact**: [one sentence]
```

### Detailed Record Format

```yaml
project: [name]
issue: [number]
pr: [number]
started: [date]
merged: [date]
difficulty: [easy/medium/hard]
type: [bug/feature/docs]

timeline:
  - date: [date]
    event: [what happened]

learnings:
  technical: [list]
  process: [list]

retrospective:
  went_well: [list]
  could_improve: [list]
```

## Arguments

`$ARGUMENTS` can include:
- Issue/PR: `#123` or `pr#456`
- Format: `--format=blog`, `--format=portfolio`, `--format=detailed`
- Output: `--output=./contributions/`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jay05410) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
