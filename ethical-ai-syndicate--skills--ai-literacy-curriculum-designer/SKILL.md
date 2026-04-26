---
name: ai-literacy-curriculum-designer
description: Use when designing AI education programs. Use when scaling AI adoption. Produces tiered curriculum, learning paths, and training materials framework.
metadata:
  author: ethical-ai-syndicate
---

# AI Literacy Curriculum Designer

## Overview

Design AI education programs tailored to different audiences in the organization. Create learning paths that build appropriate AI literacy from executives to practitioners.

**Core principle:** Different roles need different AI knowledge. Tailor education to what each audience needs to know and do.

## When to Use

- Launching AI education program
- Onboarding new employees
- Preparing for AI tool rollout
- Building internal AI capability
- Addressing knowledge gaps

## Output Format

```yaml
ai_curriculum:
  program_name: "[Name]"
  version: "[Version]"
  last_updated: "[YYYY-MM-DD]"
  
  learning_paths:
    - path_id: "[PATH-001]"
      name: "[Path name]"
      target_audience: "[Who this is for]"
      objective: "[What learners will achieve]"
      prerequisites: ["[Prerequisites]"]
      duration: "[Total hours]"
      
      modules:
        - module_id: "[MOD-001]"
          title: "[Module title]"
          format: "[e-learning | Workshop | Hands-on | etc.]"
          duration: "[Hours]"
          
          learning_objectives:
            - "[What learners will be able to do]"
          
          topics:
            - "[Topic 1]"
            - "[Topic 2]"
          
          activities:
            - type: "[Lecture | Exercise | Discussion | Project]"
              description: "[What they do]"
          
          assessment:
            type: "[Quiz | Project | Demo | None]"
            passing_criteria: "[What constitutes pass]"
          
          resources:
            - type: "[Video | Reading | Tool access]"
              name: "[Resource name]"
              location: "[Where to find]"
      
      certification:
        available: [true | false]
        requirements: ["[Completion requirement]"]
        validity: "[How long valid]"
  
  audience_matrix:
    - audience: "[Role/Level]"
      recommended_path: "[PATH-ID]"
      mandatory: [true | false]
      deadline: "[If applicable]"
  
  delivery:
    platforms: ["[LMS | Workshop | etc.]"]
    schedule: "[When offered]"
    support: "[How to get help]"
  
  metrics:
    completion_target: "[%]"
    satisfaction_target: "[Score]"
    competency_assessment: "[How measured]"
```

## Audience Segmentation

### Executive/Leadership
```yaml
executive_path:
  name: "AI for Leaders"
  duration: "2-4 hours"
  format: "Workshop + reading"
  
  objectives:
    - "Understand AI capabilities and limitations"
    - "Identify strategic AI opportunities"
    - "Make informed AI investment decisions"
    - "Lead AI governance"
  
  topics:
    - "AI fundamentals (no-code, concept level)"
    - "Business impact and ROI"
    - "Risks and governance"
    - "Leading AI transformation"
    - "Competitive landscape"
  
  not_covered:
    - "Technical implementation"
    - "Hands-on tools"
    - "Algorithm details"
```

### Managers/Business Users
```yaml
manager_path:
  name: "AI for Business"
  duration: "8-12 hours"
  format: "e-learning + workshop"
  
  objectives:
    - "Identify AI opportunities in your domain"
    - "Work effectively with AI teams"
    - "Evaluate AI project proposals"
    - "Manage AI-augmented teams"
  
  topics:
    - "AI capabilities by type"
    - "Use case identification"
    - "Data requirements"
    - "Working with AI teams"
    - "Change management for AI"
    - "AI ethics and policies"
```

### End Users
```yaml
end_user_path:
  name: "Working with AI"
  duration: "2-4 hours"
  format: "e-learning + guided practice"
  
  objectives:
    - "Use approved AI tools effectively"
    - "Understand AI limitations"
    - "Follow AI policies"
    - "Report issues appropriately"
  
  topics:
    - "How AI works (conceptual)"
    - "Prompt engineering basics"
    - "Reviewing AI outputs"
    - "Do's and don'ts"
    - "Company AI policy"
```

### AI Practitioners
```yaml
practitioner_path:
  name: "AI Development"
  duration: "40+ hours"
  format: "Hands-on + projects"
  
  objectives:
    - "Build production AI systems"
    - "Follow development best practices"
    - "Implement responsible AI"
    - "Monitor and maintain AI"
  
  topics:
    - "ML fundamentals"
    - "LLM application development"
    - "Prompt engineering advanced"
    - "Evaluation and testing"
    - "MLOps and deployment"
    - "Responsible AI implementation"
```

## Module Design Template

### Module Structure
```yaml
module_template:
  overview:
    - "Learning objectives (3-5)"
    - "Prerequisites"
    - "Time commitment"
  
  content:
    - type: "Concept introduction"
      method: "Video or reading"
      duration: "10-15 min"
    
    - type: "Examples/demos"
      method: "Walkthrough"
      duration: "15-20 min"
    
    - type: "Hands-on practice"
      method: "Guided exercise"
      duration: "20-30 min"
    
    - type: "Knowledge check"
      method: "Quiz or discussion"
      duration: "10 min"
  
  wrap_up:
    - "Key takeaways"
    - "Resources for deeper learning"
    - "Next module preview"
```

### Learning Objective Format
```
Action verb + specific content + context

Examples:
- "Identify three AI use cases in your workflow"
- "Write effective prompts for document summarization"
- "Explain AI limitations to stakeholders"
- "Evaluate AI vendor proposals against requirements"
```

## Delivery Methods

| Method | Best For | Audience |
|--------|----------|----------|
| **e-Learning** | Foundation knowledge, scale | All |
| **Workshop** | Discussion, application | Managers, execs |
| **Hands-on lab** | Skill building | Practitioners, users |
| **Coaching** | Deep skill development | Practitioners |
| **Lunch & learn** | Awareness, culture | All |
| **Office hours** | Q&A, support | All |

## Assessment Approaches

### Knowledge (Know)
```yaml
knowledge_assessment:
  - method: "Quiz"
    when: "End of module"
    passing: "80%"
  
  - method: "Discussion responses"
    when: "During workshop"
    rubric: "Quality of reasoning"
```

### Skills (Do)
```yaml
skills_assessment:
  - method: "Hands-on project"
    when: "End of path"
    rubric: "Working solution that meets criteria"
  
  - method: "Prompt portfolio"
    when: "End of user path"
    rubric: "5 effective prompts with rationale"
```

### Application (Apply)
```yaml
application_assessment:
  - method: "Use case proposal"
    when: "Post-training"
    rubric: "Viable AI opportunity identified"
  
  - method: "Manager observation"
    when: "On the job"
    rubric: "Using AI tools appropriately"
```

## Program Metrics

```yaml
program_metrics:
  reach:
    - "% of target audience enrolled"
    - "% of target audience completed"
  
  quality:
    - "Learner satisfaction (NPS or rating)"
    - "Assessment pass rates"
    - "Time to completion"
  
  impact:
    - "AI tool adoption rate"
    - "Use cases identified post-training"
    - "Reduction in AI support requests"
```

## Checklist

- [ ] Audiences segmented with needs
- [ ] Learning paths designed per audience
- [ ] Modules have clear objectives
- [ ] Content appropriately detailed
- [ ] Hands-on practice included
- [ ] Assessments defined
- [ ] Delivery method selected
- [ ] Resources identified/created
- [ ] Success metrics defined

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ethical-ai-syndicate) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
