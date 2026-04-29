---
name: prototyping
description: Master prototyping - Figma, wireframes, interactive prototypes, user testing, iteration Use when this capability is needed.
metadata:
  author: pluginagentmarketplace
---

# Prototyping Skill

> **Atomic Skill**: Create rapid, testable prototypes that validate design decisions

## Purpose

This skill provides structured approaches to wireframing, prototyping, and design validation.

## Skill Invocation

```
Skill("custom-plugin-ux-design:prototyping")
```

## Parameter Schema

### Input Parameters
```typescript
interface PrototypingParams {
  // Required
  type: "wireframe" | "prototype" | "test" | "iterate";
  scope: string;

  // Optional
  fidelity?: "low" | "medium" | "high";
  platform?: "web" | "mobile" | "desktop";
  tool?: "figma" | "sketch" | "framer" | "html";
  testing?: {
    participants: number;
    scenarios: string[];
  };
}
```

### Validation Rules
```yaml
type:
  type: enum
  required: true
  values: [wireframe, prototype, test, iterate]

scope:
  type: string
  required: true
  min_length: 5

fidelity:
  type: enum
  default: "medium"

testing.participants:
  type: number
  min: 3
  max: 50
  default: 5
```

## Execution Flow

```
PROTOTYPING EXECUTION
────────────────────────────────────────────

Step 1: DEFINE SCOPE
├── Identify key flows
├── Prioritize screens
└── Set fidelity level

Step 2: CREATE WIREFRAMES
├── Sketch layouts
├── Define content blocks
└── Map navigation

Step 3: BUILD PROTOTYPE
├── Apply visual design
├── Add interactions
└── Connect flows

Step 4: TEST WITH USERS
├── Prepare test script
├── Conduct sessions
└── Collect feedback

Step 5: ITERATE
├── Analyze findings
├── Prioritize changes
└── Update prototype

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
    - SYNC_FAILED
    - EXPORT_TIMEOUT
    - SESSION_INTERRUPTED
```

## Logging Hooks

```typescript
interface PrototypeLog {
  timestamp: string;
  event: "wireframe_created" | "prototype_built" | "test_started" | "iteration_complete";
  screens_count: number;
  interactions_count: number;
  test_sessions: number;
  success_rate: number;
}
```

## Learning Modules

### Module 1: Wireframing
```
WIREFRAME TYPES
├── Sketch (paper, whiteboard)
├── Low-fidelity (grayscale, boxes)
├── Mid-fidelity (structure, some detail)
└── High-fidelity (near-final)

WIREFRAME COMPONENTS
├── Layout grids
├── Content placeholders
├── Navigation elements
├── Form elements
└── Annotations
```

### Module 2: Figma Workflows
```
FIGMA BEST PRACTICES
├── File organization
│   ├── Pages for flows
│   ├── Frames for screens
│   └── Components for reuse
├── Component structure
│   ├── Base components
│   ├── Variants
│   └── Instances
├── Prototyping
│   ├── Connections
│   ├── Interactions
│   └── Animations
└── Collaboration
    ├── Comments
    ├── Dev mode
    └── Handoff
```

### Module 3: Interactive Prototyping
```
INTERACTION TYPES
├── Click/tap navigation
├── Hover states
├── Scroll behaviors
├── Input interactions
├── Animations
└── Smart animate

PROTOTYPE FLOWS
├── Happy path (success)
├── Error paths
├── Edge cases
└── Empty states
```

### Module 4: User Testing
```
TEST PREPARATION
├── Define objectives
├── Write test script
├── Prepare scenarios
├── Set up recording
└── Recruit participants

TEST EXECUTION
├── Introduction (5 min)
├── Tasks (20-30 min)
├── Debrief (5 min)
└── Documentation

ANALYSIS
├── Task success rates
├── Time on task
├── Error frequency
├── Satisfaction ratings
└── Qualitative insights
```

### Module 5: Iteration Cycles
```
ITERATION FRAMEWORK
├── Collect feedback
├── Identify patterns
├── Prioritize changes
├── Implement updates
├── Validate improvements
└── Document decisions

ITERATION VELOCITY
├── Quick wins (< 1 hour)
├── Standard changes (< 1 day)
├── Major revisions (< 1 week)
└── Structural changes (> 1 week)
```

## Error Handling

| Error Code | Description | Recovery |
|------------|-------------|----------|
| `PT-001` | Incomplete flow | Add missing screens |
| `PT-002` | Broken link | Fix connections |
| `PT-003` | Test invalid | Refine methodology |
| `PT-004` | Scope exceeded | Refocus on MVP |
| `PT-005` | Sync failed | Manual backup |

## Troubleshooting

### Problem: Users confused during testing
```
Diagnosis:
├── Check: Task clarity
├── Check: Prototype completeness
├── Check: Missing affordances
└── Solution: Improve setup

Steps:
1. Review task wording
2. Add interaction hints
3. Fill in missing screens
4. Brief users properly
```

### Problem: Prototype doesn't match design
```
Diagnosis:
├── Check: Design system sync
├── Check: Component versions
├── Check: Manual overrides
└── Solution: Resync sources

Steps:
1. Update component library
2. Replace broken instances
3. Remove manual overrides
4. Verify with design team
```

## Unit Test Templates

```typescript
describe("PrototypingSkill", () => {
  describe("wireframe creation", () => {
    it("should include all specified screens", async () => {
      const result = await invoke({
        type: "wireframe",
        scope: "onboarding flow",
        screens: ["welcome", "signup", "profile", "complete"]
      });
      expect(result.screens.length).toBe(4);
    });
  });

  describe("prototype interactions", () => {
    it("should connect all screens", async () => {
      const result = await invoke({
        type: "prototype",
        scope: "checkout flow"
      });
      expect(result.orphaned_screens).toHaveLength(0);
    });
  });

  describe("test execution", () => {
    it("should generate success metrics", async () => {
      const result = await invoke({
        type: "test",
        testing: { participants: 5, scenarios: ["complete purchase"] }
      });
      expect(result.metrics.task_success_rate).toBeDefined();
    });
  });
});
```

## Quality Metrics

| Metric | Target | Measurement |
|--------|--------|-------------|
| Flow coverage | > 90% | Screens connected |
| Interaction completeness | > 95% | States defined |
| Test success rate | > 80% | Task completion |
| Iteration velocity | < 24h | Time per cycle |

## Version History

| Version | Date | Changes |
|---------|------|---------|
| 1.0.0 | 2025-12-30 | Production-grade upgrade |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/pluginagentmarketplace) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
