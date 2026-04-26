---
name: user-research-synthesizer
description: Use when consolidating user feedback from multiple sources. Use after interviews, surveys, or support tickets collected. Produces synthesized insights, patterns, persona updates, and prioritized opportunities.
metadata:
  author: ethical-ai-syndicate
---

# User Research Synthesizer

## Overview

Transform raw user feedback from multiple sources into actionable insights. Synthesizes interviews, surveys, support tickets, and behavioral data into patterns that inform product decisions.

**Core principle:** Individual feedback is anecdotal. Synthesized patterns are actionable. Always seek the signal in the noise.

## When to Use

- After completing user interview rounds
- Synthesizing quarterly survey results
- Analyzing support ticket themes
- Combining qualitative and quantitative data
- Informing roadmap prioritization

## Output Format

```yaml
research_synthesis:
  period: "[Date range]"
  sources:
    - type: "[Interviews | Survey | Support tickets | Analytics]"
      count: "[N]"
      segments_covered: ["[Segment A]", "[Segment B]"]
  
  key_insights:
    - id: "INS-01"
      insight: "[Clear, actionable insight statement]"
      confidence: "[High | Medium | Low]"
      evidence:
        - source: "[Source type]"
          data_points: "[N]"
          representative_quote: "[Verbatim quote]"
      affected_segments: ["[Segment]"]
      opportunity_size: "[Large | Medium | Small]"
      recommended_action: "[What to do about it]"
  
  patterns:
    themes:
      - theme: "[Recurring theme]"
        frequency: "[How often it appeared]"
        sentiment: "[Positive | Neutral | Negative]"
        verbatims:
          - "[Quote 1]"
          - "[Quote 2]"
    
    pain_points:
      - pain_point: "[User frustration]"
        severity: "[High | Medium | Low]"
        frequency: "[How common]"
        current_workaround: "[How users cope]"
    
    unmet_needs:
      - need: "[What users want but don't have]"
        jobs_to_be_done: "[Underlying job]"
        alternatives_used: "[Current solutions]"
  
  segment_findings:
    - segment: "[User segment]"
      distinct_behaviors: ["[Behavior 1]", "[Behavior 2]"]
      distinct_needs: ["[Need 1]", "[Need 2]"]
      satisfaction_level: "[High | Medium | Low]"
  
  persona_updates:
    - persona: "[Persona name]"
      validated: ["[Assumption confirmed]"]
      invalidated: ["[Assumption disproved]"]
      new_learnings: ["[New insight]"]
  
  opportunity_prioritization:
    high_impact:
      - opportunity: "[Opportunity description]"
        insights: ["INS-01", "INS-02"]
        estimated_impact: "[Business value]"
        effort_estimate: "[Relative effort]"
    
    quick_wins:
      - opportunity: "[Opportunity description]"
        insights: ["INS-03"]
    
    needs_more_research:
      - opportunity: "[Opportunity description]"
        open_questions: ["[What we still don't know]"]
  
  methodology_notes:
    sample_size_adequacy: "[Assessment]"
    bias_considerations: ["[Potential bias 1]"]
    confidence_limitations: ["[Where conclusions are weak]"]
```

## Insight Quality Framework

### Strong Insights
| Attribute | Description | Example |
|-----------|-------------|---------|
| **Specific** | Names the problem clearly | "Users abandon onboarding at step 3" |
| **Actionable** | Implies what to do | "...because the form is too long" |
| **Supported** | Evidence from multiple sources | "5/7 interviewees + 23% survey mentions" |
| **Surprising** | Reveals something new | Not just confirming assumptions |

### Weak Insights
| Red Flag | Example | Fix |
|----------|---------|-----|
| Too vague | "Users want it to be better" | Specify what "better" means |
| Single source | "One user said..." | Seek pattern across sources |
| Assumption confirmation | "As we expected..." | Note bias, seek disconfirming |
| No action | "Interesting to note..." | Add recommended action |

## Source-Specific Synthesis

### User Interviews
```yaml
interview_synthesis:
  total_interviews: 12
  segments_represented:
    power_users: 4
    new_users: 5
    churned_users: 3
  
  affinity_mapping:
    group_1:
      theme: "Onboarding confusion"
      quotes:
        - "I didn't know where to start" - New user, Enterprise
        - "The getting started guide assumes too much" - New user, SMB
      insight: "Onboarding assumes product familiarity we can't assume"
```

### Survey Results
```yaml
survey_synthesis:
  response_rate: "23% (245/1067)"
  statistical_significance: "Yes at 95% CI"
  
  quantitative_highlights:
    - question: "How satisfied are you with X?"
      score: 3.2/5
      trend: "Down from 3.8 last quarter"
      segment_variance: "Enterprise 4.1, SMB 2.8"
  
  qualitative_themes:
    - theme: "Feature X is confusing"
      mentions: 47
      sentiment_score: -0.6
```

### Support Tickets
```yaml
ticket_synthesis:
  period: "Q4 2025"
  total_tickets: 1,234
  
  category_breakdown:
    - category: "How-to questions"
      volume: 456 (37%)
      trending: "Up 15%"
      implication: "Documentation gap or UX issue"
    
    - category: "Bug reports"
      volume: 234 (19%)
      top_feature: "Export function (45 tickets)"
```

## Pattern Recognition

### Theme Clustering
Group related feedback:

```
Theme: "Integration Difficulties"
├── "API documentation is incomplete" (7 mentions)
├── "Webhook reliability issues" (5 mentions)
├── "No sandbox environment" (4 mentions)
└── "Authentication flow is confusing" (3 mentions)

Synthesized Insight: "Developer experience for integrations 
is a significant friction point, with documentation and 
testing environments as the primary gaps."
```

### Jobs-to-Be-Done Analysis
```yaml
jtbd_analysis:
  - job: "When I get a new customer, I want to set them up quickly"
    current_solution: "Manual data entry + spreadsheet tracking"
    pain_points:
      - "Takes 30+ minutes per customer"
      - "Easy to make mistakes"
      - "No visibility into status"
    opportunity: "Automated customer onboarding workflow"
    evidence_strength: "Strong (12/15 interviewees mentioned)"
```

## Bias Mitigation

### Common Biases
| Bias | Risk | Mitigation |
|------|------|------------|
| **Confirmation** | Hearing what we expect | Seek disconfirming evidence |
| **Recency** | Over-weighting recent feedback | Look at trends over time |
| **Vocal minority** | Loud voices dominate | Quantify frequency |
| **Survivorship** | Only hearing from current users | Include churned users |
| **Leading questions** | Interviewer influenced answers | Review question framing |

### Confidence Assessment
```yaml
confidence_assessment:
  high_confidence:
    criteria: "5+ sources, consistent pattern, quantitative support"
    insights: ["INS-01", "INS-04"]
  
  medium_confidence:
    criteria: "3-4 sources, some variance, qualitative only"
    insights: ["INS-02", "INS-05"]
  
  low_confidence:
    criteria: "1-2 sources, emerging pattern, needs validation"
    insights: ["INS-03", "INS-06"]
```

## Output Formats

### Executive Summary
```markdown
## Research Summary: Q4 User Feedback

**Key Finding:** Users love core functionality but struggle with 
setup and integration. 67% satisfaction overall, but onboarding 
NPS is -15.

**Top 3 Insights:**
1. Onboarding takes 3x longer than users expect (HIGH confidence)
2. API documentation gaps block developer adoption (HIGH confidence)
3. Power users want bulk operations (MEDIUM confidence)

**Recommended Actions:**
- Redesign onboarding (Q1 priority)
- API documentation sprint (quick win)
- Bulk operations research spike (validate demand)
```

### Detailed Report
Full synthesis with all evidence and methodology notes.

### Insight Cards
One-page summaries for individual insights, suitable for roadmap discussions.

## Synthesis Checklist

Before finalizing:

- [ ] Multiple sources consulted
- [ ] Patterns quantified (not just listed)
- [ ] Confidence levels assigned
- [ ] Biases acknowledged
- [ ] Segment differences noted
- [ ] Insights are actionable
- [ ] Persona updates identified
- [ ] Opportunities prioritized
- [ ] Open questions documented

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ethical-ai-syndicate) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
