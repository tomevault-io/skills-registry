---
name: prompt-library-curator
description: Use when organizing and sharing prompts across teams. Use after prompts developed. Produces prompt registry, versioning system, quality ratings, and usage guidelines.
metadata:
  author: ethical-ai-syndicate
---

# Prompt Library Curator

## Overview

Organize, version, and share effective prompts across the organization. Maintain quality standards, track usage, and enable prompt reuse.

**Core principle:** Good prompts are organizational assets. Curate them like code—versioned, documented, and shared.

## When to Use

- Building shared prompt repository
- Standardizing prompts across teams
- Documenting successful prompt patterns
- Onboarding new prompt engineers

## Output Format

```yaml
prompt_library:
  version: "[Library version]"
  last_updated: "[YYYY-MM-DD]"
  
  prompts:
    - id: "[PROMPT-XXX]"
      name: "[Descriptive name]"
      version: "[X.Y.Z]"
      
      metadata:
        author: "[Creator]"
        created: "[YYYY-MM-DD]"
        updated: "[YYYY-MM-DD]"
        category: "[Classification | Extraction | Generation | etc.]"
        tags: ["[tag1]", "[tag2]"]
        status: "[Draft | Active | Deprecated]"
      
      prompt:
        system: |
          [System prompt content]
        
        user_template: |
          [User prompt template with {{variables}}]
        
        variables:
          - name: "{{variable_name}}"
            description: "[What it represents]"
            type: "[string | list | json]"
            required: [true | false]
            example: "[Example value]"
      
      model_compatibility:
        tested_models: ["[Model 1]", "[Model 2]"]
        recommended_model: "[Best performing model]"
        parameters:
          temperature: "[Value]"
          max_tokens: "[Value]"
          other: "[Other params]"
      
      performance:
        quality_rating: "[1-5]"
        reliability: "[Consistent | Variable | Unstable]"
        latency: "[Fast | Medium | Slow]"
        cost_tier: "[Low | Medium | High]"
      
      usage:
        use_cases: ["[Use case 1]", "[Use case 2]"]
        anti_patterns: ["[When NOT to use]"]
        example_inputs: ["[Example]"]
        example_outputs: ["[Example]"]
      
      evaluation:
        criteria: ["[How to judge quality]"]
        test_cases:
          - input: "[Test input]"
            expected: "[Expected output pattern]"
      
      related_prompts: ["[PROMPT-XXX]"]
      
      changelog:
        - version: "[X.Y.Z]"
          date: "[YYYY-MM-DD]"
          changes: "[What changed]"
  
  categories:
    - name: "[Category name]"
      description: "[What prompts in this category do]"
      prompt_count: "[N]"
  
  guidelines:
    quality_standards: "[Link or embedded standards]"
    contribution_process: "[How to add new prompts]"
    review_process: "[How prompts are reviewed]"
```

## Prompt Categories

| Category | Purpose | Examples |
|----------|---------|----------|
| **Classification** | Categorize inputs | Sentiment, intent, topic |
| **Extraction** | Pull structured data | Entity extraction, parsing |
| **Generation** | Create content | Summaries, drafts, responses |
| **Transformation** | Convert formats | Translation, reformatting |
| **Analysis** | Evaluate or assess | Quality scoring, comparison |
| **Reasoning** | Multi-step thinking | Problem solving, planning |

## Prompt Quality Standards

### Quality Rating (1-5)
| Rating | Criteria |
|--------|----------|
| 5 | Production-ready, tested extensively, documented |
| 4 | Reliable, good coverage, minor edge cases |
| 3 | Works for main cases, needs refinement |
| 2 | Experimental, inconsistent results |
| 1 | Draft, untested |

### Quality Checklist
```yaml
quality_checklist:
  clarity:
    - "Instructions are unambiguous"
    - "Expected output format is clear"
    - "Edge cases are addressed"
  
  robustness:
    - "Tested with diverse inputs"
    - "Handles malformed input gracefully"
    - "Consistent outputs across runs"
  
  documentation:
    - "Purpose clearly stated"
    - "Variables documented"
    - "Examples provided"
    - "Anti-patterns noted"
```

## Versioning Strategy

### Semantic Versioning for Prompts
```
v[MAJOR].[MINOR].[PATCH]

MAJOR: Significant behavior change, output format change
MINOR: New capabilities, improved handling
PATCH: Typo fixes, minor wording improvements
```

### When to Version
| Change | Version Impact |
|--------|---------------|
| Output format change | MAJOR |
| New variable added | MINOR |
| Better edge case handling | MINOR |
| Typo fix | PATCH |
| Wording clarification | PATCH |

## Prompt Template Best Practices

### Structure
```yaml
prompt_structure:
  role_definition: "Who the AI is acting as"
  context: "Background information needed"
  task: "What to do"
  constraints: "Rules and limitations"
  output_format: "How to structure the response"
  examples: "Few-shot examples if needed"
```

### Example Template
```markdown
# System Prompt Template

You are a {{role}} helping with {{task_domain}}.

## Your Task
{{task_description}}

## Guidelines
- {{guideline_1}}
- {{guideline_2}}

## Output Format
Respond with a JSON object:
```json
{
  "field1": "description",
  "field2": "description"
}
```

## Examples
Input: {{example_input}}
Output: {{example_output}}
```

## Usage Tracking

```yaml
usage_metrics:
  prompt_id: "[PROMPT-XXX]"
  period: "[Month/Quarter]"
  
  metrics:
    invocations: "[Count]"
    unique_users: "[Count]"
    teams_using: ["[Team 1]", "[Team 2]"]
    
  quality_feedback:
    thumbs_up: "[Count]"
    thumbs_down: "[Count]"
    comments: ["[Feedback]"]
    
  issues_reported: "[Count]"
  improvements_suggested: ["[Suggestion]"]
```

## Contribution Process

```yaml
contribution_workflow:
  1_draft:
    - "Create prompt following template"
    - "Document variables and examples"
    - "Self-test with diverse inputs"
  
  2_review:
    - "Submit for peer review"
    - "Address feedback"
    - "Test with reviewer's inputs"
  
  3_approval:
    - "Quality rating assigned"
    - "Category and tags confirmed"
    - "Added to library with Draft status"
  
  4_promotion:
    - "Pilot with limited users"
    - "Gather feedback"
    - "Promote to Active if successful"
```

## Common Anti-Patterns

| Anti-Pattern | Problem | Fix |
|--------------|---------|-----|
| Vague instructions | Inconsistent outputs | Be specific about expectations |
| No output format | Unparseable responses | Specify exact format needed |
| Missing examples | Model guesses intent | Add few-shot examples |
| Too many constraints | Model gets confused | Prioritize key constraints |
| Outdated prompts | Uses deprecated patterns | Regular review and updates |

## Checklist

- [ ] Prompt follows template structure
- [ ] All variables documented
- [ ] Examples provided (input and output)
- [ ] Model compatibility tested
- [ ] Quality rating assigned
- [ ] Category and tags applied
- [ ] Version tracked
- [ ] Usage guidelines documented

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ethical-ai-syndicate) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
