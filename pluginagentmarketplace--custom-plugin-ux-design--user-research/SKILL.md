---
name: user-research
description: Master user research methods - interviews, surveys, personas, journey mapping, usability testing Use when this capability is needed.
metadata:
  author: pluginagentmarketplace
---

# User Research Skill

> **Atomic Skill**: Conduct comprehensive user research to inform design decisions

## Purpose

This skill provides structured methodologies for understanding users through qualitative and quantitative research methods.

## Skill Invocation

```
Skill("custom-plugin-ux-design:user-research")
```

## Parameter Schema

### Input Parameters
```typescript
interface UserResearchParams {
  // Required
  method: "interview" | "survey" | "persona" | "journey_map" | "usability_test";
  objective: string;

  // Optional
  participants?: {
    count: number;
    criteria: string[];
    recruitment_source?: string;
  };
  timeline?: string;
  deliverables?: string[];
}
```

### Validation Rules
```yaml
method:
  type: enum
  required: true
  values: [interview, survey, persona, journey_map, usability_test]

objective:
  type: string
  required: true
  min_length: 10
  max_length: 500

participants.count:
  type: number
  min: 1
  max: 100
  default: 5
```

## Execution Flow

```
USER RESEARCH EXECUTION
────────────────────────────────────────────

Step 1: VALIDATE INPUTS
├── Check method validity
├── Verify objective clarity
└── Validate participant criteria

Step 2: PLAN RESEARCH
├── Select appropriate method
├── Design research protocol
└── Prepare materials

Step 3: EXECUTE
├── Conduct research sessions
├── Collect data
└── Document observations

Step 4: ANALYZE
├── Synthesize findings
├── Identify patterns
└── Generate insights

Step 5: DELIVER
├── Create deliverables
├── Present findings
└── Recommend actions

────────────────────────────────────────────
```

## Retry Logic

```yaml
retry_config:
  max_attempts: 3
  backoff_type: exponential
  initial_delay_ms: 1000
  max_delay_ms: 10000
  retryable_errors:
    - PARTICIPANT_NO_SHOW
    - SESSION_INTERRUPTED
    - DATA_INCOMPLETE
```

## Logging Hooks

```typescript
// Execution logging
interface ResearchLog {
  timestamp: string;
  event: "start" | "step_complete" | "error" | "complete";
  method: string;
  participants_completed: number;
  insights_generated: number;
  duration_ms: number;
}

// Log events
onStart: log({ event: "start", method, objective });
onStepComplete: log({ event: "step_complete", step, data });
onError: log({ event: "error", error, context });
onComplete: log({ event: "complete", summary });
```

## Learning Modules

### Module 1: Interview Techniques
```
INTERVIEW FUNDAMENTALS
├── Semi-structured interviews
├── Open-ended questions
├── Active listening
├── Probing techniques
└── Note-taking methods

ADVANCED TECHNIQUES
├── Contextual inquiry
├── Think-aloud protocol
├── Critical incident technique
└── Laddering interviews
```

### Module 2: Survey Design
```
SURVEY BEST PRACTICES
├── Question types (Likert, multiple choice, open)
├── Survey flow and logic
├── Bias prevention
├── Response rate optimization
└── Statistical validity
```

### Module 3: Persona Creation
```
PERSONA FRAMEWORK
├── Research-based personas
├── Behavioral archetypes
├── Goals and motivations
├── Pain points and frustrations
├── Jobs to be done
└── Validation methods
```

### Module 4: Journey Mapping
```
JOURNEY MAP COMPONENTS
├── Stages and touchpoints
├── Actions and behaviors
├── Thoughts and feelings
├── Pain points and opportunities
├── Moments of truth
└── Service blueprints
```

### Module 5: Usability Testing
```
USABILITY TEST PROTOCOL
├── Task design
├── Success metrics
├── Moderation techniques
├── Observation methods
├── Analysis frameworks
└── Reporting templates
```

## Error Handling

| Error Code | Description | Recovery |
|------------|-------------|----------|
| `UR-001` | Invalid method | Suggest valid methods |
| `UR-002` | Insufficient participants | Recommend recruitment strategies |
| `UR-003` | Unclear objective | Prompt for clarification |
| `UR-004` | Data quality issue | Flag and document |
| `UR-005` | Analysis blocked | Provide partial insights |

## Troubleshooting

### Problem: Low participant recruitment
```
Diagnosis:
├── Check: Screening criteria too narrow?
├── Check: Incentive sufficient?
├── Check: Recruitment channels appropriate?
└── Solution: Expand criteria or channels

Steps:
1. Review screening criteria
2. Adjust incentive structure
3. Add recruitment sources
4. Extend timeline if needed
```

### Problem: Biased findings
```
Diagnosis:
├── Check: Leading questions?
├── Check: Sample representative?
├── Check: Moderator influence?
└── Solution: Audit methodology

Steps:
1. Review question wording
2. Check sample demographics
3. Use neutral facilitation
4. Triangulate with other data
```

## Unit Test Templates

```typescript
describe("UserResearchSkill", () => {
  describe("parameter validation", () => {
    it("should reject invalid method", () => {
      expect(() => invoke({ method: "invalid" }))
        .toThrow("UR-001");
    });

    it("should require objective", () => {
      expect(() => invoke({ method: "interview" }))
        .toThrow("objective is required");
    });
  });

  describe("interview execution", () => {
    it("should generate interview script", async () => {
      const result = await invoke({
        method: "interview",
        objective: "Understand onboarding experience"
      });
      expect(result.deliverables).toContain("interview_script");
    });
  });

  describe("persona creation", () => {
    it("should create research-based persona", async () => {
      const result = await invoke({
        method: "persona",
        objective: "Define primary user archetype"
      });
      expect(result.persona).toHaveProperty("goals");
      expect(result.persona).toHaveProperty("pain_points");
    });
  });
});
```

## Quality Metrics

| Metric | Target | Measurement |
|--------|--------|-------------|
| Sample validity | > 80% | Representative coverage |
| Insight actionability | > 90% | Stakeholder adoption |
| Finding confidence | > 85% | Triangulation score |
| Participant satisfaction | > 4/5 | Post-session rating |

## Version History

| Version | Date | Changes |
|---------|------|---------|
| 1.0.0 | 2025-12-30 | Production-grade upgrade |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/pluginagentmarketplace) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
