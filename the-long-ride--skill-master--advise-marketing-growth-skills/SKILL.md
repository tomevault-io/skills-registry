---
name: advise-marketing-growth-skills
description: Use when a human wants to design, evaluate, or improve AI agent skills for marketing, growth, campaigns, content strategy, SEO, brand messaging, lifecycle emails, competitive positioning, or go-to-market workflows.
metadata:
  author: the-long-ride
---

# Marketing And Growth Skill Advisor

Advise the human on skills that help marketing teams create grounded campaigns, messaging, and growth experiments.

## Workflow

1. Identify the marketing workflow: campaign planning, content brief, SEO, lifecycle message, positioning, competitive analysis, launch messaging, or experiment design.
2. Define audience, channel, offer, brand voice, and success metric.
3. Specify source materials: brand guide, product facts, approved claims, customer segments, analytics, and competitive sources.
4. Separate ideation drafts from publishing or sending.
5. Define claim validation and compliance review.
6. Return a marketing-skill blueprint with artifact examples.

## Category Standards

- Ground claims in approved product and brand sources.
- Define audience and channel before writing.
- Separate creative options from final copy.
- Avoid unsupported performance, legal, or competitor claims.
- Include measurement or experiment criteria where relevant.
- Preserve brand voice and required disclaimers.

## Checklist

- Trigger description names marketing, growth, SEO, campaign, content, brand, GTM, email, positioning, or launch messaging.
- Workflow defines audience, channel, and success metric.
- Skill defines claim review and approval boundaries.
- Failure modes cover missing brand voice, unsupported claims, stale competitor info, and accidental publishing.
- Output format matches the marketing artifact.
- Validation includes a risky claim or wrong-audience prompt.

## Nice To Have

- Brand voice reference.
- Messaging house.
- Campaign brief template.
- SEO checklist.
- Experiment design template.

## Example Skill Ideas

- Campaign brief skill.
- Lifecycle email drafter skill.
- SEO content planner skill.
- Positioning review skill.

## Failure Modes

- Brand guidance is missing: ask for audience, tone, and examples before final copy.
- Claim is unsupported: rewrite as a weaker claim or request evidence.
- Regulated topic appears: route through legal or compliance review.
- Publishing action is requested: require explicit confirmation and destination.

## Output Format

Return:

```text
Skill concept: <name>
Marketing workflow: <workflow>
Trigger description draft: <Use when...>
Audience and channel: <summary>
Claim safeguards:
- <rule>
Validation:
- <check>
Failure recovery:
- <mode and recovery>
```

---
> Source: [the-long-ride/skill-master](https://github.com/the-long-ride/skill-master) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
