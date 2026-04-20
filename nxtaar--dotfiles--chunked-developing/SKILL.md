---
name: chunked-developing
description: Default code generation approach with incremental chunks and mandatory human verification. Use for ALL code writing - application code, infrastructure, configs, scripts, deployments. Explains architectural decisions before each chunk and waits for explicit approval. This is the preferred coding workflow. Use when this capability is needed.
metadata:
  author: nxtaar
---

# Chunked Developing

**Default coding workflow**: Write code incrementally in small chunks, explain decisions, verify with user before proceeding.

## When to Activate

**Always** - This is the preferred approach for writing any code:
- Application code (any language)
- Infrastructure-as-Code (Terraform, Kubernetes, Docker, Ansible)
- Configuration files (YAML, JSON, TOML)
- Scripts (Bash, Python, etc.)
- CI/CD pipelines
- Database migrations
- API definitions
- Test files

## Core Principle

**Never generate more than one logical chunk without explicit user approval.**

Each chunk must:
1. Explain the architectural decision BEFORE showing code
2. Show code (50-75 lines max)
3. STOP and WAIT for approval
4. Only proceed after explicit "yes" / "continue" / "approved"

## Chunk Size Guidelines

| Content Type | Max Lines | Rationale |
|--------------|-----------|-----------|
| Application code | 50-75 | Reviewable in single screen |
| Infrastructure (Terraform/K8s) | 30-50 | High-impact, needs scrutiny |
| Security code (auth, crypto) | 25-40 | Critical, every line matters |
| Configuration files | 40-60 | Structure must be clear |
| Database migrations | 20-30 | Destructive potential |
| CI/CD pipelines | 30-40 | Affects all deployments |

## Process

### 1. Pre-Generation Planning (MANDATORY)

Before writing ANY code, present:

```markdown
## Implementation Overview

### What We're Building
[1-2 sentence summary]

### Proposed Chunks
| # | Chunk | Lines | Risk Level |
|---|-------|-------|------------|
| 1 | [Description] | ~XX | Low/Med/High |
| 2 | [Description] | ~XX | Low/Med/High |
...

### Key Architectural Decisions
1. [Decision]: [Rationale]
2. [Decision]: [Rationale]

### Alternatives Considered
- [Option A]: Rejected because [reason]
- [Option B]: Rejected because [reason]

**Approve this plan before I begin? [yes/no]**
```

**STOP AND WAIT** for plan approval.

### 2. Per-Chunk Protocol

For EACH chunk, follow this exact sequence:

#### A. Pre-Chunk Explanation (BEFORE code)
```markdown
## Chunk [N] of [Total]: [Title]

### What This Chunk Does
[2-3 sentences explaining purpose]

### Architectural Decision
**Choice:** [What we're doing]
**Why:** [Reasoning]
**Trade-off:** [What we gain vs. what we give up]

### How It Connects
- **Depends on:** [Previous chunks/components]
- **Enables:** [Future chunks/components]

### Alternatives NOT Chosen
| Option | Rejected Because |
|--------|------------------|
| [Alt 1] | [Reason] |
| [Alt 2] | [Reason] |
```

#### B. Generate Chunk (50-75 lines max)
```[language]
[Code here - one complete logical unit]
```

#### C. Post-Chunk Verification (MANDATORY PAUSE)
```markdown
### Verification Checkpoint

**Questions for you:**
1. Does this match your expectations for [specific aspect]?
2. Any concerns about [key decision made]?
3. Should I adjust [configurable aspect]?

**Status:** Chunk [N] of [Total] complete

**Approve this chunk?** [yes / no / revise]

⏸️ **WAITING FOR YOUR RESPONSE**
```

**STOP AND WAIT** - Do NOT proceed without explicit approval.

#### D. Handle Response
- **"yes" / "approved" / "continue"** → Record chunk, proceed to next
- **"revise" / feedback** → Regenerate chunk with adjustments
- **"no" / "stop"** → Discuss alternatives, do NOT proceed

### 3. Natural Chunk Boundaries

Break at these logical points:

**Application Code:**
- Type/interface definitions (foundation)
- Function signatures (contracts)
- Core logic implementation
- Error handling
- Edge cases
- Tests

**Infrastructure (Terraform):**
1. Provider & backend config
2. Variables & locals
3. Data sources
4. Networking resources
5. Compute resources
6. Security groups / IAM
7. Outputs

**Kubernetes:**
1. Namespace & ConfigMaps
2. Secrets & ServiceAccounts
3. Deployments
4. Services
5. Ingress / NetworkPolicies
6. HPA / PDB / Resource quotas

**Docker:**
1. Base image & args
2. Dependencies installation
3. Build steps
4. Configuration
5. Entrypoint / CMD
6. Health checks

**CI/CD Pipelines:**
1. Triggers & environment
2. Build stage
3. Test stage
4. Security/lint stage
5. Deploy stage
6. Notifications / cleanup

## Output Templates

### Chunk Output Format
```markdown
---
## Chunk [N]/[Total]: [Descriptive Title]

### Decision Rationale
**Approach:** [What we're implementing]
**Why this way:** [Technical reasoning]
**Trade-off accepted:** [Pro vs con]

### Code

\`\`\`[language]
[50-75 lines of code]
\`\`\`

### What This Enables
- [Next chunk can now...]
- [This integrates with...]

---

⏸️ **CHECKPOINT** - Approve to continue? [yes/no/revise]
```

### IaC Risk-Based Chunking

For Terraform/Kubernetes, chunk by blast radius:

| Order | Resource Type | Risk | Why This Order |
|-------|--------------|------|----------------|
| 1 | Data sources | LOW | Read-only, no state change |
| 2 | Locals/variables | LOW | No infrastructure impact |
| 3 | Namespaces/orgs | LOW | Organizational only |
| 4 | Networking | MEDIUM | May affect traffic flow |
| 5 | Compute | MEDIUM | Cost implications |
| 6 | IAM/Security | HIGH | Privilege escalation risk |
| 7 | Databases/Storage | CRITICAL | Data loss potential |

## Progress Tracking

Maintain state for session continuity:

```json
{
  "mode": "chunked",
  "file": "path/to/file",
  "total_chunks": 6,
  "approved_chunks": [
    {"id": 1, "title": "Types", "approved": true, "lines": 45},
    {"id": 2, "title": "Core logic", "approved": true, "lines": 62}
  ],
  "current_chunk": 3,
  "decisions_log": [
    {"chunk": 1, "decision": "Use interfaces over types", "rationale": "..."},
    {"chunk": 2, "decision": "Async over sync", "rationale": "..."}
  ],
  "user_feedback": [
    {"chunk": 2, "feedback": "Add error logging", "addressed": true}
  ]
}
```

## Integration with Other Skills

| Skill | Integration Point |
|-------|-------------------|
| **planning** | Can mark tasks as `mode: chunked` |
| **developing** | Delegates large tasks here |
| **debugging** | Called if chunk causes issues |
| **verifying** | Runs after final chunk approved |

## When to Exit Chunked Mode

After final chunk approval:
1. Show complete implementation summary
2. List all decisions made
3. Offer to run verification commands
4. Return control to user or `developing` skill

```markdown
## Implementation Complete

### Summary
- **Total chunks:** [N]
- **Total lines:** [X]
- **Files created/modified:** [list]

### Decisions Made
1. [Decision 1]: [Rationale]
2. [Decision 2]: [Rationale]

### Ready for Verification?
Shall I run verification commands or invoke the **verifying** skill?
```

## Best Practices

1. **Explain before you code** - Architecture rationale comes first
2. **One logical unit per chunk** - Not arbitrary line counts
3. **Explicit pauses** - Never assume approval
4. **Track all decisions** - Build a decision log
5. **Incorporate feedback** - Adjust future chunks based on input
6. **Risk-aware ordering** - Lower risk chunks first
7. **Show connections** - How chunks relate to each other

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/nxtaar) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
