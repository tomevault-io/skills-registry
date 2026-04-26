---
name: demo-script-preparer
description: Use when preparing sprint demos or stakeholder presentations. Use before demo meeting. Produces demo flow, talking points, anticipated questions, and backup scenarios.
metadata:
  author: ethical-ai-syndicate
---

# Demo Script Preparer

## Overview

Prepare effective sprint demos and stakeholder presentations that showcase value, not just features. Create smooth demo flows with talking points and fallback plans.

**Core principle:** A demo is a story about value delivered. The feature is evidence, not the point.

## When to Use

- Sprint review preparation
- Stakeholder update meetings
- Sales demo preparation
- Executive presentations
- Customer success reviews

## Output Format

```yaml
demo_script:
  title: "[Demo title]"
  date: "[YYYY-MM-DD]"
  duration: "[Minutes]"
  audience: "[Who will be watching]"
  
  objective:
    primary: "[Main takeaway for audience]"
    secondary: ["[Additional takeaway]"]
  
  context:
    what_we_committed: "[Sprint/project goals]"
    what_we_delivered: "[Actual delivery]"
    value_message: "[Why this matters]"
  
  demo_flow:
    - sequence: 1
      title: "[Scene/feature name]"
      duration: "[Minutes]"
      environment: "[URL/app/environment to use]"
      
      setup:
        pre_conditions: "[State before demo starts]"
        test_data: "[What data should exist]"
        login_as: "[User/role]"
      
      walkthrough:
        - step: "[Action to take]"
          narration: "[What to say]"
          highlight: "[What to point out]"
      
      key_message: "[Value statement for this scene]"
      
      transition: "[How to move to next scene]"
  
  talking_points:
    opening:
      - "[Opening statement 1]"
    closing:
      - "[Summary statement]"
      - "[Call to action]"
  
  questions_anticipated:
    - question: "[Likely question]"
      answer: "[Prepared response]"
      supporting_info: "[Data/examples to cite]"
  
  backup_plans:
    - if: "[Something goes wrong]"
      then: "[Fallback action]"
      script: "[What to say]"
  
  logistics:
    presenter: "[Who presents]"
    tech_support: "[Who handles tech issues]"
    recording: "[Yes/No]"
    follow_up: "[Post-demo action]"
```

## Demo Structure

### Standard Sprint Demo (15-20 min)
```
0:00 - 2:00   Context & Objectives
              "Here's what we set out to do..."

2:00 - 12:00  Demo Walkthrough
              [Show features with value framing]

12:00 - 15:00 Metrics & Outcomes
              "Here's the impact we're seeing..."

15:00 - 18:00 Coming Next
              "Here's what's planned..."

18:00 - 20:00 Questions
```

### Executive Demo (10 min)
```
0:00 - 2:00   Business Context
              "The problem we solved..."

2:00 - 7:00   Key Capability Demo
              [One powerful flow, not multiple features]

7:00 - 9:00   Results & Value
              "Here's what this enables..."

9:00 - 10:00  Questions
```

## Effective Narration

### Feature → Benefit Framing
| Don't Say | Do Say |
|-----------|--------|
| "We added a filter button" | "Users can now find what they need in seconds instead of scrolling through everything" |
| "This is the new dashboard" | "This gives operations managers one view of everything they need each morning" |
| "Click here to export" | "When finance needs data for their monthly close, one click gets the report they need" |

### Transition Phrases
```yaml
transitions:
  between_features:
    - "Now that we've solved [X], let me show you how we addressed [Y]..."
    - "Building on that capability..."
  
  from_problem_to_solution:
    - "Before this sprint, users had to [pain]. Now..."
    - "We heard from [X users] that [problem]. So we built..."
  
  to_close:
    - "So in summary, users can now..."
    - "The net impact is..."
```

## Question Preparation

### Anticipate by Audience
| Audience | Likely Questions |
|----------|-----------------|
| **Executive** | "What's the business impact?" "When can all users have this?" |
| **Technical** | "How does this scale?" "What about edge case X?" |
| **Operations** | "How does this change our process?" "What training is needed?" |
| **Customer** | "When will this be in production?" "Can we beta test?" |

### Handling Unknowns
```yaml
graceful_deflection:
  template: |
    "That's a great question. I don't have the exact answer right now,
    but I'll follow up with you by [timeframe]. What I can tell you is..."
  
  example: |
    "I don't have performance benchmarks at scale yet. We tested with
    [X records] and saw [Y performance]. I'll get production data
    and share it by end of week."
```

## Backup Plans

### Common Issues & Responses

| Issue | Backup | Script |
|-------|--------|--------|
| Feature doesn't load | Switch to screenshots/recording | "Let me show you this—we have a recording that shows the full flow" |
| Wrong data appears | Have test account ready | "Let me switch to our demo environment which has cleaner data" |
| Network issues | Have offline backup | "While we reconnect, let me walk through this on slides" |
| Time running short | Jump to conclusion | "In the interest of time, let me summarize the remaining capabilities..." |

## Pre-Demo Checklist

### Day Before
- [ ] Run through full demo once
- [ ] Verify test data exists
- [ ] Check all environments accessible
- [ ] Prepare backup materials (screenshots, recording)
- [ ] Send calendar reminder to audience

### 30 Minutes Before
- [ ] Close unnecessary apps
- [ ] Open all needed tabs/apps
- [ ] Log into test accounts
- [ ] Hide notifications
- [ ] Test screen share

### 5 Minutes Before
- [ ] Navigate to starting point
- [ ] Have talking points visible
- [ ] Water ready
- [ ] Tech support on standby

## Quality Checklist

- [ ] Demo tells a story, not just shows features
- [ ] Each scene has clear value message
- [ ] Narration uses benefit framing
- [ ] Time estimates are realistic
- [ ] Questions anticipated and prepared
- [ ] Backup plans for likely failures
- [ ] Test data is appropriate for audience
- [ ] Logistics confirmed with participants

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ethical-ai-syndicate) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
