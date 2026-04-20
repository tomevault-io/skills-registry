---
name: spec-orchestrator
description: Multi-agent specification review and iteration. Writes specs via spec-writer, reviews with AI personas, and iterates until approved. Use when this capability is needed.
metadata:
  author: ky1ejs
---

# Spec Orchestrator

Coordinates the full lifecycle of specification development: writing, multi-agent review, revision, and approval. Simulates diverse team perspectives during review and iterates until the spec is approved or escalated to the user.

## Arguments

- `topic-or-spec` (positional): A topic to write a spec for, or path to existing spec
- `--skip-review`: Write spec without review cycle (just invoke spec-writer)
- `--reviewers=<p1,p2>`: Use only specific personas (comma-separated names)
- `--max-iterations=<n>`: Override max revision cycles
- `--require-approval`: Always require human approval (never auto-approve)

## Configuration

Check for `.claude/spec-workflow/config.yaml`:

```yaml
review:
  maxIterations: 3              # Cycles before escalating to human
  autoApproveThreshold: 0.8     # Consensus for auto-approval (0.0-1.0)
  selection:
    strategy: "auto"            # "auto" | "all" | "explicit"
    minimum: 2                  # Minimum reviewers
    maximum: 4                  # Maximum reviewers
```

### Philosophy Injection

If `.claude/spec-workflow/philosophy/review-criteria.md` exists, inject it into all reviewer prompts to guide their focus.

### Custom Personas

If `.claude/spec-workflow/personas/` exists and contains `.md` files:
- Load each file as a reviewer persona
- Parse frontmatter for `name` and `triggers`
- Use these instead of built-in personas

Otherwise, use built-in personas from references/personas.md.

---

## Modes

1. **Full loop**: Write spec → review → revise → re-review until approved
2. **Review only**: Review an existing spec and provide feedback

---

## Flow

```
Input (idea or spec)
     │
     ▼
┌─────────────────────┐
│ B: Is spec written? │
└─────────────────────┘
     │
     ├─No──► Invoke spec-writer (autonomous mode)
     │              │
     │              ▼
     └─Yes─► C: Does spec need agent review?
                   │
                   ├─No (trivial)──► Human review
                   │
                   └─Yes──► Parallel agent review
                                  │
                                  ▼
                           D: Evaluate feedback
                                  │
                   ┌──────────────┼──────────────┐
                   ▼              ▼              ▼
               Approved      Iterate         Escalate
                   │              │              │
                   ▼              │              ▼
                 EXIT             │        Human review
                                  │              │
                                  └──────────────┘
```

---

## State

Track across iterations:

```
{
  iteration: 0,
  maxIterations: 3,           // From config or argument
  status: "drafting" | "awaiting-review" | "in-review" | "approved",
  spec: null,
  reviewPath: "agent" | "human" | null,
  originalRequest: string,
  history: [{ iteration, feedback, reviewer }],
  escalationCount: 0
}
```

---

## Coordinator Decisions

### B: Is spec written?

- **No** → Invoke spec-writer with `{ mode: "autonomous", request: originalRequest }`
- **Yes** → Evaluate if agent review needed (C)

### C: Does spec need agent review?

Classify the spec first, then:

| Criteria | Route |
|----------|-------|
| Trivial scope AND low risk | **No** → Human review |
| Small/medium scope OR medium risk | **Yes** → Agent review |
| Large scope OR high risk | **Yes** → Agent review |

### D: Does feedback need iteration?

Evaluate feedback and return one of:

**Approved** → Exit
- No "must address" items, OR
- All reviewers LGTM, OR
- Consensus meets `autoApproveThreshold`

**Iterate** → Back to spec-writer
- Has "must address" items that can be addressed
- Under `maxIterations`

**Escalate** → Human Review
- Max iterations reached
- Reviewers fundamentally disagree
- Spec writer disputes feedback
- Feedback requires judgment beyond spec scope

---

## Classification

Analyze the spec and determine:

**Type** (select one):
- `bug-fix` — Fixing incorrect behavior
- `small-feature` — Additive, limited scope
- `large-feature` — Significant new capability
- `refactor` — Restructuring without behavior change
- `infrastructure` — Build, deploy, observability
- `api-change` — Public interface modifications

**Risk** (select one):
- `low` — Easily reversible, limited blast radius
- `medium` — Some complexity, moderate impact
- `high` — Hard to undo, broad impact, security-sensitive

**Scope** — Based on surface area and complexity:

| Scope | Surface Area | Complexity Signals |
|-------|--------------|-------------------|
| Trivial | Single function/method | None |
| Small | Single file/module | 0-1 signals |
| Medium | Multiple files or 2-3 components | 1-2 signals |
| Large | Multiple services, external deps, or 4+ components | 2+ signals |

Complexity signals: new abstractions, data model changes, state management, concurrency, new dependencies.

---

## Agent Review

When the spec needs agent review, invoke reviewer personas in parallel.

### Select Reviewers

Based on classification, determine review depth:

| Criteria | Depth |
|----------|-------|
| Small/medium scope OR medium risk | **Standard** (2-4 reviewers) |
| Large scope OR high risk | **Deep** (all reviewers) |

**Standard review** — select based on type:

| Type | Reviewers |
|------|-----------|
| bug-fix | Paranoid, Simplifier |
| small-feature | Simplifier, User Advocate |
| refactor | Architect, Paranoid, Simplifier |
| infrastructure | Architect, Paranoid, Operator |
| api-change | Architect, Paranoid, Simplifier, User Advocate |

**Deep review** — all available reviewers

If `--reviewers` argument provided, use only those personas.

### Execute in Parallel

Reviewers are independent—run as parallel sub-agents using the Task tool:

```
FOR EACH reviewer IN selectedReviewers:
  SPAWN_SUBAGENT(
    prompt: [Persona prompt] + [Review criteria philosophy] + [Spec content],
    description: "{reviewer.name} reviewing spec"
  )

AWAIT_ALL(tasks)
RETURN SYNTHESIZE(results)
```

### Reviewer Prompt Template

```markdown
[Persona prompt from personas file]

---

## Review Criteria

[Content from philosophy/review-criteria.md if present]

---

## Spec to Review

**Classification**: {type} | {risk} risk | {scope} scope

{spec content}

---

Provide your review. If you have no substantive feedback, respond only with:
"No concerns from a {perspective} perspective—LGTM."
```

### Synthesize Feedback

After all reviews, produce:

```markdown
## Review Summary

[2-3 sentence overview]

### Must Address
- [Issue] — raised by [Reviewer]

### Should Consider
- [Suggestion] — raised by [Reviewer]

### Minor/Optional
- [Item] — raised by [Reviewer]

### Points of Disagreement
- [Topic]: [Reviewer A] says X, [Reviewer B] says Y
```

---

## Human Review

### Standard Review

For simple specs that don't need agent review:

```markdown
## Spec Ready for Review

[spec content]

---

Please review. When done, provide:
1. Any issues that must be addressed (blocking)
2. Suggestions to consider (non-blocking)
3. Your verdict: **approve** or **request changes**
```

### Escalation Review

When escalated from agent review:

```markdown
## Spec Escalated for Human Review

### Escalation Reason
[max-iterations | reviewer-disagreement | spec-writer-dispute | requires-judgment]

[Details]

### Iteration History
[Summary of each round]

---

### Current Spec
[spec content]

---

Please provide direction:
1. **Approve** if acceptable
2. **Feedback** for another iteration
3. **Redirect** if approach is fundamentally wrong
```

---

## Spec Writer Integration

This skill invokes spec-writer directly. See references/spec-writer-integration.md for the contract.

**Autonomous draft**: `{ mode: "autonomous", request: string }`

**Revision**: `{ mode: "revision", currentSpec: string, feedback: object, iteration: number }`

---

## Output

### Approved Spec

```markdown
## Spec Approved

**Review path**: Agent | Human | Mixed
**Review depth**: Light | Standard | Deep
**Iterations**: N

---

[Final spec content]

---

## Review Discussion

### Key Feedback Addressed
[Issues raised and how resolved, with reviewer attribution]

### Tradeoffs Considered
[Alternatives discussed, why rejected]

### Dissenting Perspectives
[Concerns acknowledged but not fully addressed]

---

### Review History
[What changed each iteration]

### Escalations
[If any, what was escalated and how resolved]
```

---

## Configuration Options

| Option | Default | Description |
|--------|---------|-------------|
| `review.maxIterations` | 3 | Iterations before escalating |
| `review.autoApproveThreshold` | 0.8 | Auto-approve if consensus ≥ this |
| `review.selection.strategy` | "auto" | How to select reviewers |
| `review.selection.minimum` | 2 | Minimum reviewers |
| `review.selection.maximum` | 4 | Maximum reviewers |

---

## Built-in Personas

When no custom personas are configured, use:

- **Pragmatic Architect** — System design, integration, maintainability
- **Paranoid Engineer** — Failure modes, edge cases, security
- **Operator** — Observability, deployment, incident response
- **Simplifier** — YAGNI, minimal scope, clarity
- **User Advocate** — DX/UX, documentation, mental models
- **Product Strategist** — Customer value, success metrics, opportunity cost

See references/personas.md for full prompts.

---

## Error Handling

**Spec writer fails**: Retry once with clarified prompt. If still failing, escalate to human.

**Reviewer fails**: Skip and continue with others. Note in synthesis.

**All reviewers fail**: Escalate to human review.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ky1ejs) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
