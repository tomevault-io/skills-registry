---
name: user-story-expertise
description: Expert knowledge for writing INVEST-compliant user stories with complete technical context. Reference material for transforming planning notes into developer-ready stories. Use when this capability is needed.
metadata:
  author: srirajk
---

# User Story Writing Expertise

This skill captures the knowledge of expert product owners who write high-quality, technically detailed user stories.

## INVEST Criteria

Every user story must follow INVEST:

- **Independent**: Can be developed in any order, minimal dependencies
- **Negotiable**: Details can be discussed, not a rigid contract
- **Valuable**: Delivers clear value to end user or business
- **Estimable**: Team can estimate the effort required
- **Small**: Fits within a single sprint/iteration
- **Testable**: Has clear acceptance criteria to verify completion

## User Story Format

### Title/Summary
```
As a [user type], I want [goal] so that [benefit]
```

### Full Story Structure (Jira)
```
*Summary:*
As a [user type], I want [goal] so that [benefit]

*Description:*

h3. User Story
As a [user type], I want [goal] so that [benefit]

h3. Context
[Business need, pain points, metrics]

h3. Technical Details
*Domain:* [Team name]
*Team Contact:* [Lead email, Slack channel]
*Tech Stack:* [Technologies]
*Files to Modify:*
• {{path/to/file.js:line}} - [what changes]

h3. Acceptance Criteria
[ ] [Criterion 1]
[ ] [Criterion 2]

h3. Dependencies & Coordination
[Teams to coordinate with]

h3. Testing Strategy
[Unit, integration, E2E tests needed]

h3. Rollout Plan
[Deployment strategy, rollback plan]

h3. Monitoring
[Metrics to track, alerts]
```

## Knowledge Graph Query Patterns

When gathering context, use these MCP query patterns:

### 1. Find Service Ownership
```python
# Query: Who owns this service?
get_neighbors(service_name, filter={"relationship": "owns"})
# Returns: Team node with lead, slack, domains
```

### 2. Find Implementation Files
```python
# Query: Which files implement this service?
get_neighbors(service_name, filter={"relationship": "implemented_in"})
# Returns: File nodes with paths
```

### 3. Find Functions in Files
```python
# Query: What functions are in this file?
get_neighbors(file_path, filter={"relationship": "contains"})
# Returns: Function nodes with line numbers
```

### 4. Find Service Dependencies
```python
# Query: What does this service depend on?
get_neighbors(service_name, filter={"relationship": "depends_on"})
# Returns: Dependent service nodes
```

### 5. Find Impacted Teams
```python
# For each dependent service, find its owner
for dep_service in dependencies:
    get_neighbors(dep_service, filter={"relationship": "owns"})
# Returns: Teams that need coordination
```

### 6. Get Approved Technologies
```python
# Query: What's in the golden stack?
query_graph(filter={"type": "technology", "approved": true})
# Returns: Node.js 20, PostgreSQL 15, etc.
```

### 7. Get Architecture Patterns
```python
# Query: What patterns should we follow?
query_graph(filter={"type": "pattern"})
# Returns: Retry patterns, error handling, etc.
```

### 8. Get Development Standards
```python
# Query: What conventions apply?
query_graph(filter={"type": "convention", "category": "api-design"})
query_graph(filter={"type": "standard", "category": "testing"})
# Returns: API conventions, testing requirements
```

## Technical Detail Requirements

Stories MUST include specific technical details:

### File Paths and Line Numbers
- ✅ `src/services/payment-processor.js:45-89` - specific range
- ❌ `payment-processor.js` - too vague

### Function Names
- ✅ `processPayment()` function at line 45
- ❌ "the payment function" - not specific enough

### Existing Code Context
- Reference existing patterns in the code
- Point to TODO comments if present
- Mention related functions/classes

### Team Coordination
- List exact teams by name
- Include Slack channels
- Specify what needs coordination (API changes, message formats, etc.)

## Acceptance Criteria Guidelines

Write 5-7 testable criteria covering:

1. **Functional requirements** (features that must work)
2. **Non-functional requirements** (performance, security)
3. **Testing requirements** (coverage, E2E scenarios)
4. **Monitoring requirements** (metrics, alerts)
5. **Documentation requirements** (what docs to update)

Each criterion must be:
- Specific (not vague)
- Measurable (can verify it's done)
- Actionable (clear what to do)

### Examples

❌ Bad: "System should be fast"
✅ Good: "API responds in < 500ms at p95"

❌ Bad: "Add tests"
✅ Good: "Unit tests cover retry success, partial failure, and complete failure scenarios (80% coverage minimum)"

❌ Bad: "Handle errors properly"
✅ Good: "Transient errors (408, 429, 503) retry up to 3 times; permanent errors (400, card_error) fail immediately"

## Quality Checklist

Before finalizing a story, verify:

- [ ] Title follows "As a..., I want..., so that..." format
- [ ] Context explains WHY (business need, user pain)
- [ ] Team ownership identified from knowledge graph
- [ ] Specific file paths with line numbers included
- [ ] All service dependencies identified
- [ ] Coordination needs specified (which teams, why)
- [ ] Acceptance criteria are testable and measurable
- [ ] Testing strategy covers unit, integration, E2E
- [ ] Rollout plan includes deployment strategy and rollback
- [ ] Monitoring plan specifies metrics and alerts
- [ ] Story follows INVEST criteria
- [ ] Uses proper Jira wiki markup (h3, {{}}, checkboxes)

## Common Mistakes to Avoid

### ❌ Being Generic
- "Update error handling" → Which file? Which function? What kind of errors?

### ✅ Being Specific
- "Add exponential backoff retry logic to `processPayment()` in `payment-processor.js:67-89` for transient errors (408, 429, 503, ETIMEDOUT)"

### ❌ Missing Dependencies
- Not identifying downstream teams affected by changes

### ✅ Identifying Dependencies
- "Messaging Team (#messaging-team) must be notified - we're changing the `payment.completed` Kafka message format"

### ❌ Vague Acceptance Criteria
- "Works correctly"

### ✅ Specific Acceptance Criteria
- "Retry occurs exactly 3 times with delays of 1s, 2s, 4s (exponential backoff)"

## Workflow Summary

1. **Read** planning notes thoroughly
2. **Query** knowledge graph for all context (ownership, tech, patterns, files)
3. **Read** actual codebase files mentioned in graph
4. **Identify** specific locations (files, functions, line numbers)
5. **Map** dependencies and coordination needs
6. **Apply** INVEST criteria to structure the story
7. **Read** Jira template for formatting
8. **Generate** complete story with all sections filled
9. **Validate** using quality checklist

## Resources Referenced

- Planning notes (user-provided input)
- Knowledge graph (via MCP queries)
- Codebase files (via Read tool)
- Jira story template: `templates/jira-story-template.md`
- Architecture docs (indexed in knowledge graph)
- Golden stack (indexed in knowledge graph)
- Standards (indexed in knowledge graph)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/srirajk) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
