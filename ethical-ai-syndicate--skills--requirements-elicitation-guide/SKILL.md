---
name: requirements-elicitation-guide
description: Use when conducting stakeholder interviews for requirements. Use before requirements documentation. Produces interview scripts, probing questions, and requirement capture templates.
metadata:
  author: ethical-ai-syndicate
---

# Requirements Elicitation Guide

## Overview

Structure effective stakeholder conversations that surface real requirements, not just stated wants. Guide discovery conversations that uncover needs, constraints, and success criteria.

**Core principle:** Requirements aren't given, they're discovered. Surface the real need behind the stated request.

## When to Use

- Initial discovery for new project
- Gathering requirements from stakeholders
- Understanding user workflows
- Clarifying ambiguous feature requests
- Cross-functional requirement alignment

## Output Format

```yaml
elicitation_plan:
  initiative: "[Initiative name]"
  stakeholders:
    - name: "[Name]"
      role: "[Role]"
      perspective: "[What they represent]"
      interview_date: "[Date]"
  
  interview_structure:
    opening:
      script: "[How to start the conversation]"
      duration: "[Minutes]"
    
    exploration:
      sections:
        - section: "[Topic area]"
          questions: ["[Question 1]", "[Question 2]"]
          probing: ["[Follow-up prompts]"]
          duration: "[Minutes]"
    
    closing:
      script: "[How to end]"
      next_steps: "[What happens after]"
  
  requirements_capture:
    needs:
      - id: "REQ-001"
        statement: "[User needs to...]"
        stakeholder: "[Who expressed]"
        priority: "[Must | Should | Could]"
        validation: "[How to verify understanding]"
    
    constraints:
      - type: "[Technical | Business | Regulatory]"
        constraint: "[Description]"
        impact: "[What it limits]"
    
    success_criteria:
      - criterion: "[Measurable outcome]"
        stakeholder: "[Who defined]"
    
    open_questions:
      - question: "[What's still unclear]"
        assigned_to: "[Who will resolve]"
```

## Interview Structure

### Opening (5 minutes)
```yaml
opening_script:
  introduction: |
    "Thanks for meeting with me. I'm working on [initiative] 
    and want to understand your perspective and needs.
    There are no wrong answers—I'm here to learn."
  
  context_setting: |
    "We have about [X] minutes. I'd like to understand 
    [topic areas]. Is there anything specific you want 
    to make sure we cover?"
  
  permission: |
    "Is it okay if I take notes? I want to make sure 
    I capture your input accurately."
```

### Core Questions by Type

#### Problem Discovery
```yaml
problem_questions:
  current_state:
    - "Walk me through how you [do X] today."
    - "What triggers you to start this process?"
    - "Who else is involved?"
  
  pain_points:
    - "What's the most frustrating part of this?"
    - "Where do things typically go wrong?"
    - "What takes longer than it should?"
  
  impact:
    - "What happens when [problem] occurs?"
    - "How does this affect your day/team/customers?"
    - "If you had to put a number on the cost of this problem?"
  
  workarounds:
    - "How do you handle this today?"
    - "What tools or tricks have you developed?"
```

#### Needs Discovery
```yaml
needs_questions:
  outcomes:
    - "What would 'success' look like for you?"
    - "If we built the perfect solution, what would change?"
    - "What would you expect to do differently?"
  
  priorities:
    - "If you could only solve one thing, what would it be?"
    - "What's the minimum that would make this valuable?"
    - "What could we skip for now?"
  
  concerns:
    - "What worries you about this initiative?"
    - "What has to be true for this to work?"
    - "What could make this fail?"
```

### Probing Techniques

| Technique | When to Use | Example |
|-----------|-------------|---------|
| **The 5 Whys** | Surface root cause | "Why is that important? ... And why is that?" |
| **Specifics** | Vague statement | "Can you give me a specific example?" |
| **Quantify** | Understand scale | "How often does this happen? How long does it take?" |
| **Contrast** | Clarify meaning | "When you say 'fast', what would slow look like?" |
| **Hypothetical** | Test boundaries | "What if we could only do half of that?" |

### Closing (5 minutes)
```yaml
closing_script:
  summary: |
    "Let me play back what I heard to make sure I understood..."
  
  gaps: |
    "Is there anything we didn't cover that I should know about?"
  
  next_steps: |
    "Here's what happens next... I'll share notes for your review."
  
  referrals: |
    "Is there anyone else I should talk to about this?"
```

## Requirement Capture Template

### Requirement Statement Format
```
As a [user type],
I need [capability],
So that [business outcome].

Constraints: [What limits the solution]
Acceptance: [How we know it's done]
Priority: [Must | Should | Could]
Source: [Who requested + date]
```

### Example
```yaml
requirement:
  id: "REQ-042"
  statement: |
    As an Operations Analyst,
    I need to see all pending confirmations in one view,
    So that I can prioritize my work for the day.
  
  constraints:
    - "Must load in <3 seconds even with 500+ items"
    - "Must work on existing monitors (1920x1080)"
  
  acceptance_criteria:
    - "Shows all confirmations assigned to me"
    - "Sorted by priority and age"
    - "Filterable by counterparty and status"
  
  priority: "Must"
  source: "Jane Smith, Ops Lead, 2026-01-15"
  open_questions:
    - "Should it include items assigned to team or just me?"
```

## Stakeholder Analysis

### Pre-Interview Research
```yaml
stakeholder_prep:
  name: "[Name]"
  role: "[Title]"
  perspective: "[What they care about most]"
  history: "[Past involvement with similar projects]"
  expected_concerns: ["[Concern 1]", "[Concern 2]"]
  questions_specific_to_them: ["[Question]"]
```

### Conflicting Requirements
When stakeholders disagree:
```yaml
conflict_resolution:
  requirement: "[Conflicting area]"
  positions:
    - stakeholder: "Stakeholder A"
      position: "[What they want]"
      rationale: "[Why]"
    - stakeholder: "Stakeholder B"
      position: "[What they want]"
      rationale: "[Why]"
  resolution_approach:
    - "Bring stakeholders together to discuss"
    - "Find underlying need both share"
    - "Escalate to decision maker if needed"
```

## Anti-Patterns

| Anti-Pattern | Problem | Fix |
|--------------|---------|-----|
| Leading questions | Get desired answer, not truth | Ask open-ended |
| Solution-focused | Miss the real problem | Start with problem |
| Executive echo | Only hear senior voices | Include front-line users |
| Single stakeholder | Narrow view | Multiple perspectives |
| Note-taking only | Miss nuance | Record with permission |

## Elicitation Checklist

- [ ] Stakeholder list includes diverse perspectives
- [ ] Interview questions are open-ended
- [ ] Probing techniques prepared
- [ ] Note-taking or recording arranged
- [ ] Time allocated for each section
- [ ] Follow-up process defined
- [ ] Conflict resolution approach ready
- [ ] Summary shared for validation

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ethical-ai-syndicate) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
