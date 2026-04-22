---
name: feedback-loop
description: Automated feedback collection and learning documentation. Captures learnings from issues, generates ADRs for architecture decisions, and prompts for retrospective insights. Use when this capability is needed.
metadata:
  author: tygwan
---

# Feedback Loop Skill

Automates the collection of learnings, architectural decisions, and retrospective insights. Ensures institutional knowledge is captured and accessible.

## Usage

```bash
/feedback <command> [options]
```

### Commands

| Command | Description |
|---------|-------------|
| `learning` | Record a learning from issue/bug resolution |
| `adr` | Create Architecture Decision Record |
| `retro` | Generate retrospective template |
| `review` | Review recent learnings and decisions |

## Core Philosophy

```
┌─────────────────────────────────────────────────────────────┐
│                  FEEDBACK LOOP CYCLE                        │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│     Experience          Capture           Apply             │
│         ↓                  ↓                ↓               │
│   ┌─────────┐        ┌─────────┐      ┌─────────┐          │
│   │ Issue   │   →    │Learning │  →   │ Pattern │          │
│   │ Solved  │        │ Record  │      │ Applied │          │
│   └─────────┘        └─────────┘      └─────────┘          │
│                                             ↓               │
│   ┌─────────┐        ┌─────────┐      ┌─────────┐          │
│   │ Decision│   →    │   ADR   │  →   │ Future  │          │
│   │ Made    │        │ Created │      │Reference│          │
│   └─────────┘        └─────────┘      └─────────┘          │
│                                                             │
│              "Learn → Document → Improve"                   │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

## Commands Detail

### /feedback learning

Record a learning from resolving an issue or discovering a pattern.

```bash
/feedback learning "Database connection pooling prevents timeout errors"
/feedback learning --from-issue ISS-042
```

**Prompts:**
1. What was the problem?
2. What was the root cause?
3. What did you learn?
4. How can this be prevented/applied in the future?

**Output Location:** `docs/feedback/LEARNINGS.md`

**Format:**
```markdown
## LRN-{N}: {Title}

**Date**: {date}
**Category**: {category}
**Related**: {issue_id}

### Context
{What situation triggered this learning}

### Problem
{What went wrong or was discovered}

### Root Cause
{Why it happened}

### Learning
{What was learned}

### Application
{How to apply this in the future}

### Tags
`{tag1}` `{tag2}` `{tag3}`
```

### /feedback adr

Create an Architecture Decision Record for significant technical decisions.

```bash
/feedback adr "Use PostgreSQL over MySQL"
/feedback adr --title "Authentication Strategy" --status proposed
```

**Options:**
| Option | Description |
|--------|-------------|
| `--title` | ADR title |
| `--status` | proposed, accepted, deprecated, superseded |
| `--supersedes` | ADR number this replaces |

**Prompts:**
1. What is the context/problem?
2. What are the options considered?
3. What decision was made?
4. What are the consequences?

**Output Location:** `docs/adr/ADR-{N}-{slug}.md`

**Format:**
```markdown
# ADR-{N}: {Title}

**Status**: {status}
**Date**: {date}
**Deciders**: {names}
**Supersedes**: {adr_ref} (if applicable)

## Context

{Background and problem statement}

## Decision Drivers

- {driver_1}
- {driver_2}

## Considered Options

### Option 1: {name}
**Pros:**
- {pro_1}
**Cons:**
- {con_1}

### Option 2: {name}
**Pros:**
- {pro_1}
**Cons:**
- {con_1}

## Decision

{What was decided and why}

## Consequences

### Positive
- {positive_1}

### Negative
- {negative_1}

### Risks
- {risk_1}

## Related

- ADR-{X}: {related_title}
- {external_link}
```

### /feedback retro

Generate a retrospective template or prompt for insights.

```bash
/feedback retro                    # Generate template for current sprint
/feedback retro --milestone "v1.0" # Generate for milestone
/feedback retro --quick            # Quick 3-question retro
```

**Quick Retro Questions:**
1. What went well?
2. What could improve?
3. What will we try next?

**Full Retro Sections:**
- What went well (Keep)
- What could improve (Problem)
- What to try (Try)
- Action items with owners
- Velocity analysis

### /feedback review

Review recent learnings and decisions.

```bash
/feedback review                   # Show last 10 items
/feedback review --category bugs   # Filter by category
/feedback review --last 30d        # Last 30 days
```

**Output:**
```
📚 FEEDBACK REVIEW

## Recent Learnings (5)
┌─────────┬──────────────────────────────────────┬────────────┐
│ ID      │ Title                                │ Date       │
├─────────┼──────────────────────────────────────┼────────────┤
│ LRN-012 │ Connection pooling prevents timeouts │ 2025-01-05 │
│ LRN-011 │ Index order matters for composite    │ 2025-01-03 │
│ LRN-010 │ Use transactions for batch updates   │ 2024-12-28 │
└─────────┴──────────────────────────────────────┴────────────┘

## Recent ADRs (3)
┌─────────┬──────────────────────────────────────┬────────────┐
│ ID      │ Title                                │ Status     │
├─────────┼──────────────────────────────────────┼────────────┤
│ ADR-005 │ Use PostgreSQL for persistence       │ Accepted   │
│ ADR-004 │ JWT for API authentication           │ Accepted   │
│ ADR-003 │ Microservices vs Monolith           │ Superseded │
└─────────┴──────────────────────────────────────┴────────────┘

💡 Tip: Run `/feedback learning` after resolving issues
```

## Auto-Triggers

### Issue Resolution Trigger
When an issue is marked resolved, prompt for learning:

```bash
# After git commit with "fix:" or "closes #"
┌────────────────────────────────────────────────────────────┐
│ 💡 LEARNING PROMPT                                         │
│                                                            │
│ You just resolved an issue. Would you like to record      │
│ what you learned?                                          │
│                                                            │
│ Issue: Connection timeout in production                    │
│ Fix: Added connection pooling                              │
│                                                            │
│ [Y] Record learning  [N] Skip  [L] Later                  │
└────────────────────────────────────────────────────────────┘
```

### Architecture Change Trigger
When significant code changes detected, prompt for ADR:

```bash
# After changes to core infrastructure files
┌────────────────────────────────────────────────────────────┐
│ 📐 ADR PROMPT                                              │
│                                                            │
│ You made significant architecture changes:                 │
│ - Modified: src/core/database.ts                          │
│ - Added: src/services/cache.ts                            │
│                                                            │
│ Should this decision be documented as an ADR?             │
│                                                            │
│ [Y] Create ADR  [N] Skip  [D] Describe briefly           │
└────────────────────────────────────────────────────────────┘
```

## File Structure

```
docs/
├── feedback/
│   ├── LEARNINGS.md          # All learnings
│   └── INDEX.md              # Learning index by category
├── adr/
│   ├── INDEX.md              # ADR index
│   ├── ADR-001-database.md
│   ├── ADR-002-auth.md
│   └── template.md           # ADR template
└── retros/
    ├── sprint-1-retro.md
    └── milestone-v1-retro.md
```

## Categories

### Learning Categories
| Category | Keywords | Example |
|----------|----------|---------|
| `bugs` | fix, error, crash | Memory leak patterns |
| `performance` | slow, optimize, cache | Query optimization |
| `security` | auth, vulnerability | Input validation |
| `architecture` | design, pattern, structure | Event sourcing |
| `tooling` | build, deploy, ci | Docker multi-stage |
| `process` | workflow, team, communication | Code review practices |

### ADR Categories
| Category | When to Use |
|----------|-------------|
| `infrastructure` | Database, hosting, scaling |
| `architecture` | Patterns, structure, modules |
| `security` | Auth, encryption, compliance |
| `integration` | APIs, third-party, protocols |
| `process` | Development workflow, tools |

## Integration

### With Issue Tracking
```bash
# Reference issues in learnings
/feedback learning --from-issue GH-123

# Auto-link in commit messages
git commit -m "fix: timeout issue [LRN-012]"
```

### With Sprint Management
```bash
# Generate retro at sprint end
/sprint end  # Automatically triggers /feedback retro
```

### With /agile-sync
```bash
# Include feedback summary in sync
/agile-sync  # Shows recent learnings count
```

## Configuration

```json
{
  "feedback": {
    "auto_prompt_on_fix": true,
    "auto_prompt_on_arch_change": true,
    "learning_categories": ["bugs", "performance", "security", "architecture"],
    "adr_auto_number": true,
    "retro_template": "full",
    "review_default_count": 10
  }
}
```

## Best Practices

### For Learnings
- ✅ Record immediately while fresh
- ✅ Include specific examples
- ✅ Tag appropriately for search
- ✅ Link to related issues/PRs
- ❌ Don't skip "obvious" learnings
- ❌ Don't be too brief

### For ADRs
- ✅ Create before implementation
- ✅ Include rejected alternatives
- ✅ Update status when decisions change
- ✅ Link related ADRs
- ❌ Don't create for trivial decisions
- ❌ Don't forget consequences

### For Retros
- ✅ Hold within 24h of sprint end
- ✅ Assign owners to action items
- ✅ Follow up on previous actions
- ✅ Celebrate wins
- ❌ Don't blame individuals
- ❌ Don't skip action items

## Related Skills

| Skill | Purpose |
|-------|---------|
| `/sprint` | Sprint management with retro |
| `/agile-sync` | Include feedback in sync |
| `/doc` | General documentation |
| `/troubleshoot` | Issue investigation |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tygwan) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
