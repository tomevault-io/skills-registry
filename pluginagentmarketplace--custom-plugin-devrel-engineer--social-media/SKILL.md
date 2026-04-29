---
name: social-media
description: Social media strategy for developers including Twitter/X, LinkedIn, and community platforms Use when this capability is needed.
metadata:
  author: pluginagentmarketplace
---

# Social Media for DevRel

Build **developer-focused social media presence** that drives engagement and community growth.

## Skill Contract

### Parameters
```yaml
parameters:
  required:
    - platform: enum[twitter, linkedin, youtube, reddit, discord]
    - content_type: enum[post, thread, video, story]
  optional:
    - topic: string
    - campaign: string
```

### Output
```yaml
output:
  content:
    text: string
    hashtags: array[string]
    media_specs: object
    scheduling: datetime
```

## Platform Strategy

| Platform | Audience | Content Type |
|----------|----------|--------------|
| **Twitter/X** | Developers, tech | Quick tips, threads |
| **LinkedIn** | Professionals | Long-form, career |
| **YouTube** | Learners | Tutorials, deep dives |
| **Reddit** | Communities | Discussions, Q&A |
| **Discord** | Real-time | Support, community |

## Twitter/X Best Practices

### Thread Formula
```
Tweet 1: Hook (problem/question)
Tweet 2-5: Main content (tips/steps)
Tweet 6: Summary + CTA
```

### Engagement Tips
- Post when audience is active (9-11 AM, 7-9 PM)
- Use code screenshots (not plain text)
- Ask questions to drive replies
- Quote tweet with commentary
- Engage with replies quickly

## LinkedIn Strategy

### Post Types
1. **Personal stories** - Lessons learned
2. **Industry insights** - Trends and opinions
3. **How-to content** - Actionable tips
4. **Announcements** - News and updates

### Formatting
```
Strong opening line (hook)

Short paragraphs.
Use line breaks liberally.

• Bullet points work well
• Easy to scan

End with question or CTA

#relevanthashtags (3-5 max)
```

## Content Calendar

| Day | Platform | Content |
|-----|----------|---------|
| Mon | Twitter | Weekly thread |
| Tue | LinkedIn | Industry insight |
| Wed | Twitter | Quick tip |
| Thu | LinkedIn | Personal story |
| Fri | Twitter | Fun/community |

## Retry Logic

```yaml
retry_patterns:
  low_engagement:
    strategy: "Adjust timing, improve hook"

  algorithm_change:
    strategy: "Adapt content format"

  negative_feedback:
    strategy: "Address directly, learn"
```

## Failure Modes & Recovery

| Failure Mode | Detection | Recovery |
|--------------|-----------|----------|
| Low reach | <avg impressions | Boost with engagement |
| Negative response | Comments | Address, apologize if needed |
| Account issues | Restricted | Contact support |

## Debug Checklist

```
□ Content aligned with platform?
□ Optimal posting time?
□ Hashtags relevant?
□ Media formatted correctly?
□ Links working?
□ CTA clear?
```

## Test Template

```yaml
test_social_media:
  unit_tests:
    - test_content_format:
        assert: "Meets platform specs"
    - test_links_work:
        assert: "All links resolve"

  integration_tests:
    - test_engagement:
        assert: "Above baseline rate"
```

## Metrics to Track

- Follower growth rate
- Engagement rate (likes + comments / followers)
- Click-through rate
- Share/retweet ratio
- Profile visits

## Observability

```yaml
metrics:
  - posts_published: integer
  - engagement_rate: float
  - follower_growth: integer
  - reach: integer
```

See `assets/` for content templates.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/pluginagentmarketplace) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
