---
name: speckit-evaluate
description: Evaluate feature spec against market research to identify features to add, remove, or modify. Use after /speckit.research to finalize spec before design. Use when this capability is needed.
metadata:
  author: lofidonut3
---

# Speckit Evaluate

**Purpose:** Evaluate the clarified spec against market research findings to optimize feature set before design phase.

## When to Use

- After Phase 3.5 (Research) is complete
- Before Phase 4 (Design)
- When research.md exists

## Prerequisites

- [ ] Spec exists at `.github/speckit/specs/{feature-name}.md`
- [ ] Spec status is `clarified`
- [ ] Research exists at `.github/speckit/specs/{feature-name}/research.md`
- [ ] User has reviewed research findings

## Execution

### 1. Delegate to Architect (Opus)

Use `speckit-architect` for deep reasoning evaluation:

```
task(
  subagent_type="speckit-architect",
  description="Evaluate spec against market research",
  prompt="Evaluate this feature spec against the market research.

  **Spec Path**: {spec path}
  **Research Path**: {research.md path}

  Read both files thoroughly, then provide evaluation:

  ## 1. Features to ADD
  What's missing that competitors have or market expects?
  - Feature name
  - Rationale (why it's needed)
  - Evidence (from research - which competitor, which trend)
  - Impact: High/Medium/Low
  - Effort estimate: Small/Medium/Large

  ## 2. Features to REMOVE
  What's over-engineered, unnecessary, or doesn't align with market?
  - Feature name
  - Rationale (why remove)
  - Evidence (competitors don't have it, not a best practice, etc.)
  - Risk of keeping it

  ## 3. Features to MODIFY
  What needs adjustment based on best practices?
  - Feature name
  - Current approach (from spec)
  - Suggested change
  - Rationale and evidence
  - Impact: High/Medium/Low

  ## 4. Priority Assessment
  Categorize ALL features (existing + suggested additions):
  - **Must-Have (P0)**: Core value, competitors all have it
  - **Should-Have (P1)**: Expected by users, differentiator
  - **Nice-to-Have (P2)**: Enhancement, future consideration

  ## 5. Differentiation Strategy
  Based on research, how should this feature stand out?
  - Key differentiators to emphasize
  - Unique value proposition
  - Positioning against competitors

  Be specific and cite evidence from research.md for each recommendation."
)
```

### 2. Present Evaluation to User

For each recommendation, ask user to **Accept** or **Reject**:

```
## Features to ADD

### 1. {Feature Name}
- **Rationale**: {why}
- **Evidence**: {from research}
- **Impact**: High | **Effort**: Medium

→ [Accept] / [Reject]
User decision: ___

### 2. {Feature Name}
...

## Features to REMOVE
...

## Features to MODIFY
...
```

### 3. Record Decisions

For each suggestion:
- If **Accept**: Mark for inclusion in spec update
- If **Reject**: Record user's reasoning

### 4. Update Spec

Apply accepted changes:
- Add new features to spec
- Remove rejected features
- Modify adjusted features
- Update priority order based on assessment

### 5. Save Evaluation

Save to: `.github/speckit/specs/{feature-name}/evaluation.md`

Include:
- All recommendations
- User decisions (accept/reject)
- Reasoning for rejections
- Final priority list

### 6. Finalize

- Update spec status to `evaluated`
- Confirm with user: "Spec is now finalized. Ready to proceed to Design?"
- **STOP and wait for explicit approval**

## Output Format

```markdown
# Feature Evaluation: {Feature Name}

**Generated**: {date}
**Spec**: {spec path}
**Research**: {research path}
**Status**: Complete

## Evaluation Summary

| Category | Count | Accepted | Rejected |
|----------|-------|----------|----------|
| Add | 3 | 2 | 1 |
| Remove | 2 | 1 | 1 |
| Modify | 4 | 3 | 1 |

## Detailed Recommendations

### Features to ADD

#### 1. {Feature Name} ✅ ACCEPTED
- **Rationale**: {why}
- **Evidence**: {from research}
- **Impact**: High | **Effort**: Medium

#### 2. {Feature Name} ❌ REJECTED
- **Rationale**: {why}
- **Evidence**: {from research}
- **User Reasoning**: "{user's reason for rejection}"

### Features to REMOVE
...

### Features to MODIFY
...

## Final Priority Assessment

### P0 - Must Have
1. {feature}
2. {feature}

### P1 - Should Have
1. {feature}

### P2 - Nice to Have
1. {feature}

## Differentiation Strategy

{summary of how to position against competitors}

---
*Evaluation conducted by speckit-architect (Opus 4.5)*
*User decisions recorded: {date}*
```

## Handoff

After evaluation is complete and user approves:
- Update spec status to `evaluated`
- Next step: Phase 4 (Design Session)
- Spec is now **frozen** for design phase

## Key Principles

1. **User has final say** - AI suggests, user decides
2. **Evidence-based** - Every recommendation cites research
3. **No scope creep** - Removing features is as valuable as adding
4. **Priority clarity** - Clear P0/P1/P2 before design

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/lofidonut3) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
