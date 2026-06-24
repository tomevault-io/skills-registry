---
name: beads-orchestration
description: Multi-agent orchestration for GitHub Issues using BEADS task tracking Use when this capability is needed.
metadata:
  author: dsifry
---

# BEADS Multi-Agent Orchestration Skill

This skill coordinates a swarm of specialized AI agents to autonomously handle GitHub Issues from creation to merged PR.

---

## Quick Start

### Start Work on a GitHub Issue

```bash
# User triggers via any of:
@beads start #123
bd start 123
/project:beads-start 123
```

### Check BEADS Status

```bash
bd ready          # Show tasks ready to work
bd list           # Show all tasks
bd stats          # Show project statistics
bd doctor         # Check system health
```

---

## Agent Roster

| Agent                     | Role                           | Spawned When                       |
| ------------------------- | ------------------------------ | ---------------------------------- |
| **Issue Orchestrator**    | Main coordinator per Issue     | Issue receives `agent-ready` label |
| **Researcher Agent**      | Codebase exploration           | Orchestrator creates research task |
| **Architect Agent**       | Implementation planning        | Research complete                  |
| **Product Manager Agent** | Use case & user benefit review | Design review gate (parallel)      |
| **Designer Agent**        | UX/API design review           | Design review gate (parallel)      |
| **Security Design Agent** | Security threat modeling       | Design review gate (parallel)      |
| **CTO Agent**             | TDD readiness & plan review    | Design review gate (parallel)      |
| **Coder Agent**           | TDD implementation             | Design review gate approved        |
| **Code Review Agent**     | Internal code review           | Implementation complete            |
| **Security Auditor**      | Security review (code)         | Implementation complete            |
| **PR Shepherd**           | PR lifecycle management        | PR created                         |

See `agents/` directory for detailed agent definitions.

---

## Design Review Gate (NEW)

For complex features created via brainstorming, an automatic **Design Review Gate** ensures quality before implementation:

```
Design Document Created
        │
        ▼
┌─────────────────────────────────────────────────┐
│           DESIGN REVIEW GATE                     │
│                                                  │
│  Spawns in PARALLEL:                            │
│  • Architect Agent (technical architecture)     │
│  • Designer Agent (UX/API design)               │
│  • CTO Agent (TDD readiness)                    │
│                                                  │
│  ALL THREE must approve to proceed              │
└─────────────────────────────────────────────────┘
        │
        ├── Any NEEDS_REVISION? → Iterate on design (max 3x)
        │
       ALL APPROVED
        │
        ▼
   Create BEADS Epic → Begin Implementation
```

### Triggering the Design Review Gate

The gate is automatically triggered when:

- `superpowers:brainstorming` completes and commits a design doc
- User runs `/project:review-design <path-to-design.md>`

### Review Criteria by Agent

| Agent           | Focus Areas                                                |
| --------------- | ---------------------------------------------------------- |
| Product Manager | Use case clarity, user benefits, scope, success metrics    |
| Architect       | Service architecture, dependencies, patterns, integration  |
| Designer        | API design, UX flows, developer experience, consistency    |
| Security Design | Threat modeling, auth/authz, data protection, OWASP Top 10 |
| CTO             | TDD readiness, codebase alignment, completeness, risks     |

### Iteration Protocol

- **Max 3 iterations** before human escalation
- Each iteration: revise design → re-run all reviewers
- Escalation options: Override / Defer / Cancel

See `goodtogo:design-review-gate` skill for full details.

---

## Workflow Overview

```
GitHub Issue #123 (agent-ready label)
        │
        ▼
┌─────────────────────────────────────┐
│       Issue Orchestrator             │
│  Creates BEADS epic, delegates work  │
└─────────────────────────────────────┘
        │
        ▼
┌─────────────────────────────────────┐
│       Research Phase                 │
│  Researcher Agent explores codebase  │
└─────────────────────────────────────┘
        │
        ▼
┌─────────────────────────────────────┐
│       Planning Phase                 │
│  Architect Agent creates plan        │
└─────────────────────────────────────┘
        │
        ▼
┌───────────────────────────────────────────────────────────────┐
│              DESIGN REVIEW GATE (PARALLEL)                     │
│                                                                │
│  ┌─────────┐ ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌───────┐ │
│  │   PM    │ │ Architect│ │ Designer │ │ Security │ │  CTO  │ │
│  │(users)  │ │  (tech)  │ │ (UX/API) │ │ (threats)│ │ (TDD) │ │
│  └─────────┘ └──────────┘ └──────────┘ └──────────┘ └───────┘ │
│                                                                │
│  ALL FIVE must approve (max 3 iterations)                      │
└───────────────────────────────────────────────────────────────┘
        │
        ▼
┌─────────────────────────────────────┐
│       Implementation Phase           │
│  Coder Agent implements with TDD     │
└─────────────────────────────────────┘
        │
        ├──────────────────────────────┐
        ▼                              ▼
┌───────────────────┐    ┌───────────────────┐
│   Code Review     │    │  Security Audit   │
│   Agent           │    │  Agent            │
└───────────────────┘    └───────────────────┘
        │                              │
        └──────────────┬───────────────┘
                       ▼
┌─────────────────────────────────────┐
│   PR Creation (Auto-Shepherd)        │
│   bin/create-pr-with-shepherd.sh     │
│   → Auto-invokes pr-shepherd skill   │
└─────────────────────────────────────┘
        │
        ▼
┌─────────────────────────────────────┐
│       PR Shepherd (Automatic)        │
│  Monitors CI, handles reviews,       │
│  resolves threads automatically      │
└─────────────────────────────────────┘
        │
        ▼
┌─────────────────────────────────────┐
│       Human Approval & Merge         │
└─────────────────────────────────────┘
        │
        ▼
┌─────────────────────────────────────┐
│       Close Epic & Extract Learnings │
└─────────────────────────────────────┘
```

### Automatic PR Review Cycles

When a PR is created via `bin/create-pr-with-shepherd.sh`, the script outputs instructions to start monitoring:

1. **Creates the PR** with proper title/body
2. **Start pr-shepherd** using `/project:pr-shepherd <pr-number>`
3. **PR Shepherd monitors**: CI status, review comments, thread resolution
4. **Auto-fixes**: Lint, type errors, test failures in your code
5. **Reports when ready**: All CI green, all threads resolved

For manually-created PRs, invoke `/project:pr-shepherd <pr-number>` to start the monitoring cycle.

---

## BEADS Commands Reference

### Issue Management

```bash
# Create epic for GitHub Issue
bd create "Feature: User Auth" --type epic --issue 123

# Create task under epic
bd create "Research auth patterns" --type task --parent bd-abc123

# Add dependency
bd dep add <blocked-task> <blocking-task>

# Update status
bd update <task-id> --status open|in_progress|blocked|closed

# Close with reason
bd close <task-id> --reason "Completed successfully"
```

### Task Discovery

```bash
# Show ready (unblocked) tasks
bd ready --json

# List all tasks under epic
bd list --parent <epic-id>

# Show blocked tasks
bd blocked

# Show task details
bd show <task-id> --json
```

### Labels for Custom States

```bash
# Waiting for human input
bd label add <task-id> waiting:human

# Waiting for CI
bd label add <task-id> waiting:ci

# Agent failed, needs intervention
bd label add <task-id> agent:failed

# Review iteration tracking
bd label add <task-id> review:iteration-1
```

### Sync Operations

```bash
# Check sync status
bd sync --status

# Pull updates from main
bd sync --from-main

# Export to JSONL
bd export
```

---

## Starting Work on an Issue

### Step 1: Verify Issue is Ready

```bash
# Check Issue has agent-ready label
gh issue view 123 --json labels | jq '.labels[].name' | grep agent-ready
```

### Step 2: Create BEADS Epic

```bash
# Get Issue details
ISSUE=$(gh issue view 123 --json title,body,number)

# Create epic linked to Issue
bd create "$(echo $ISSUE | jq -r .title)" --type epic --issue 123 --json
```

### Step 3: Post Acknowledgment

```bash
gh issue comment 123 --body "🤖 Agent claiming this issue. BEADS epic created."
```

### Step 4: Spawn Issue Orchestrator

Use the Task tool to spawn the Issue Orchestrator agent:

```typescript
Task({
  subagent_type: "general-purpose",
  description: "Issue Orchestrator for #123",
  prompt: `You are the ISSUE ORCHESTRATOR agent.

Read the agent definition at:
.claude/plugins/goodtogo/skills/beads/agents/issue-orchestrator.md

Your task:
- Epic ID: <epic-id>
- GitHub Issue: #123
- Begin the orchestration workflow

Follow the workflow phases exactly as specified.`,
});
```

---

## Human Escalation Protocol

### When to Escalate

1. **Ambiguous Requirements**: Issue lacks clarity
2. **Conflicting Constraints**: Can't satisfy all requirements
3. **Risk Decision**: Security or data concerns
4. **Blocked > 1 Hour**: External dependency needed
5. **3 Failed Iterations**: Agent can't resolve issue

### How to Escalate

```bash
# Mark task as waiting
bd update <task-id> --status blocked
bd label add <task-id> waiting:human

# Post to GitHub Issue
gh issue comment <number> --body "$(cat <<'EOF'
## 🤖 Agent Request: <type>

**Task**: <task-id>
**Question**: <clear question>

### Options
1. **Option A**: <description>
2. **Option B**: <description>

### Agent Recommendation
<recommendation>

---
Reply: `@beads approve <task-id>` or `@beads respond <task-id> <option>`
EOF
)"
```

### Human Response Patterns

```bash
# Approve a blocked task
@beads approve bd-abc123

# Respond with choice
@beads respond bd-abc123 "Use option A"

# Request changes
@beads request-changes bd-abc123 "Need more error handling"

# Defer to later
@beads defer bd-abc123 "Discuss in Monday standup"
```

---

## Agent Spawning Patterns

### Sequential Spawning

```typescript
// Spawn Researcher first
const researchResult = await Task({
  subagent_type: "general-purpose",
  description: "Research for issue #123",
  prompt: researcherPrompt,
});

// Then spawn Architect with research output
const planResult = await Task({
  subagent_type: "general-purpose",
  description: "Planning for issue #123",
  prompt: architectPrompt + researchResult,
});
```

### Parallel Spawning

```typescript
// Spawn Code Review and Security Audit in parallel
const [reviewResult, securityResult] = await Promise.all([
  Task({
    subagent_type: "general-purpose",
    description: "Code review for #123",
    prompt: codeReviewPrompt,
  }),
  Task({
    subagent_type: "general-purpose",
    description: "Security audit for #123",
    prompt: securityAuditPrompt,
  }),
]);
```

---

## Knowledge Integration

### CRITICAL: Prime Before Starting Work

**ALL agents MUST prime their context before starting ANY work.** This prevents bad assumptions and ensures alignment with established patterns.

```bash
# General prime (loads critical rules + gotchas)
npx tsx scripts/beads-prime.ts

# Prime for specific files you'll modify
npx tsx scripts/beads-prime.ts --files "src/lib/services/*.ts" "src/app/api/*.ts"

# Prime for specific topic
npx tsx scripts/beads-prime.ts --keywords "authentication" "jwt"

# Prime for work type
npx tsx scripts/beads-prime.ts --work-type planning     # Before planning
npx tsx scripts/beads-prime.ts --work-type implementation  # Before coding
npx tsx scripts/beads-prime.ts --work-type review       # Before reviewing
npx tsx scripts/beads-prime.ts --work-type research     # Before exploring

# Combined (most thorough)
npx tsx scripts/beads-prime.ts --files "<files>" --keywords "<topic>" --work-type <type>
```

The prime command outputs relevant facts categorized as:

- **MUST FOLLOW**: Critical rules (NEVER/ALWAYS/MUST statements)
- **GOTCHAS**: Common pitfalls to avoid
- **PATTERNS**: Best practices for this codebase
- **DECISIONS**: Architectural choices

### After Completing Work

Run self-reflection to extract learnings:

```bash
# Fetch recent PR comments
GITHUB_TOKEN=$(gh auth token) npx tsx scripts/beads-fetch-pr-comments.ts --days 7

# Use self-reflect skill to evaluate and add learnings
/project:self-reflect
```

Or spawn Knowledge Curator agent:

```typescript
Task({
  subagent_type: "general-purpose",
  description: "Extract learnings from epic",
  prompt: `Review completed epic <epic-id> and extract learnings.

FIRST: Run \`npx tsx scripts/beads-prime.ts --work-type review\` to load context.

Then analyze:
- What patterns were used?
- What gotchas were discovered?
- What should future agents know?

Use the knowledge capture service to store learnings.`,
});
```

---

## Success Criteria Checklist

Before closing an epic, verify ALL:

- [ ] All BEADS tasks under epic are closed
- [ ] PR is created and linked to GitHub Issue
- [ ] **`gtg` returns READY status** (deterministic PR readiness gate)
  ```bash
  gtg "$PR_NUMBER" --repo "$OWNER/$REPO" --format json > /tmp/gtg.json
  STATUS=$(jq -r '.status' /tmp/gtg.json)
  # Must return READY status before proceeding
  ```
- [ ] Human has approved merge
- [ ] PR is merged to main
- [ ] GitHub Issue is closed
- [ ] Learnings extracted to knowledge base

### gtg as the Deterministic Gate

The `gtg` command replaces manual verification of:
- All CI checks passing
- All PR comments addressed
- All PR threads resolved

**A PR is NOT ready until `gtg` returns READY status.**

```bash
# Check PR readiness deterministically (recommended: parse JSON status)
gtg 123 --repo owner/repo --format json > /tmp/gtg.json
STATUS=$(jq -r '.status' /tmp/gtg.json)
echo "Status: $STATUS"  # Must be READY

# Alternative: use semantic exit codes for shell scripts
gtg 123 --repo owner/repo -q  # Exit code 0 = READY

# For details on what's blocking:
gtg 123 --repo owner/repo --format json | jq '.action_items'
```

---

## Troubleshooting

### Task Stuck in Progress

```bash
# Check task status
bd show <task-id> --json

# Check for orphaned agent
# If agent failed, reset and retry
bd update <task-id> --status open
bd label remove <task-id> agent:failed
```

### Circular Dependencies

```bash
# Run doctor to detect
bd doctor

# If found, restructure dependencies
bd dep remove <task1> <task2>
```

### BEADS Sync Issues

```bash
# Check sync status
bd sync --status

# Force export
bd export

# Pull from main
bd sync --from-main
```

---

## Directory Structure

```
.claude/plugins/goodtogo/skills/beads/
├── SKILL.md                    # This file
└── agents/
    ├── issue-orchestrator.md   # Main coordinator
    ├── researcher-agent.md     # Codebase exploration
    ├── architect-agent.md      # Implementation planning
    ├── product-manager-agent.md # Use case & user benefit review (NEW)
    ├── designer-agent.md       # UX/API design review (NEW)
    ├── security-design-agent.md # Security threat modeling (NEW)
    ├── cto-agent.md            # TDD readiness review
    ├── coder-agent.md          # TDD implementation
    ├── code-review-agent.md    # Internal code review
    ├── security-auditor-agent.md # Security review (implementation)
    └── pr-shepherd-agent.md    # PR lifecycle management

.claude/plugins/goodtogo/skills/design-review-gate/
└── SKILL.md                    # Design review gate orchestrator (NEW)

.claude/plugins/goodtogo/skills/brainstorming-extension/
└── SKILL.md                    # Hooks brainstorming to review gate (NEW)

.claude/commands/
└── review-design.md            # /project:review-design command (NEW)

.claude/rubrics/
├── plan-review-rubric.md       # Used by CTO Agent
└── code-review-rubric.md       # Used by Code Review Agent

.beads/
├── beads.db                    # SQLite database
├── issues.jsonl                # Issue/task data
└── knowledge/                  # Curated learnings
    ├── codebase-facts.jsonl
    ├── patterns.jsonl
    └── anti-patterns.jsonl
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/dsifry) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
