---
name: audit
description: > Use when this capability is needed.
metadata:
  author: acampb
---

# Audit Orchestrator

Validate completed work through independent review agents, fix issues, and loop until green.
Three sequential phases: acceptance criteria, architecture compliance, skill compliance.

## Sub-Commands

| Command | Purpose |
|---------|---------|
| `/audit` | Run all three phases in sequence |
| `/audit ac` | Run Phase 1 only (acceptance criteria) |
| `/audit arch` | Run Phase 2 only (architecture compliance) |
| `/audit skill` | Run Phase 3 only (skill compliance) |

## Before Any Sub-Command

**Required reads:**

1. **Read** [references/agent-prompts.md](references/agent-prompts.md) - Agent prompt templates
2. **Read** [references/warn-resolution-tracking.md](references/warn-resolution-tracking.md) - WARN tracking system design
3. **Read** `CLAUDE.md` (if exists) - Project conventions and audit config
4. **Read** `.claude/audits/overrides.yaml` (if exists) - Standing overrides for agent prompts
5. **Read** `.claude/audits/warns/agent-context/*.md` (if exists) - Past decisions for agent context
6. **Locate audit inputs** using the resolution rules below

### Resolving Audit Inputs

The audit needs three categories of input. Each has smart defaults that can be
overridden in `CLAUDE.md` under a `## Audit Configuration` section.

#### 1. Acceptance Criteria (Phase 1)

| Resolution Order | Source |
|------------------|--------|
| **Default** | `docs/roadmap/jira/*.jira.md` (if exists) — extract `**Acceptance Criteria:**` sections from stories matching the ticket IDs being audited. This is the path convention from the `/jira` skill. |
| **Fallback** | JIRA API via `mcp__atlassian__getJiraIssue` (if Atlassian MCP available) — fetch ticket descriptions and AC fields directly |
| **Last resort** | Ask user to provide acceptance criteria inline or point to a file |
| **Override** | `CLAUDE.md` → `Audit Configuration` → `acceptance_criteria_path` |

**Ticket identification:** Accept ticket IDs as arguments (e.g., `/audit PROJ-370 PROJ-371`),
or infer from the current branch (match `PROJ-\d+` pattern where PROJ comes from CLAUDE.md
JIRA config), or use `all` to audit every ticket with status "In Progress" or "Done" in the
current sprint. If `all` is used and JIRA API is unavailable, scan local `*.jira.md` files
for stories with `**Status:** In Progress` or `**Status:** Done`.

#### 2. Architecture Rules (Phase 2)

| Resolution Order | Source |
|------------------|--------|
| **Default** | `docs/architecture/*.md` (if exists) — all architecture documentation |
| **Fallback** | `CLAUDE.md` architecture sections (Technology Stack, Architecture Principles, Service Boundaries, etc.) |
| **Override** | `CLAUDE.md` → `Audit Configuration` → `architecture_docs_path` |

#### 3. Skill Rules (Phase 3)

| Resolution Order | Source |
|------------------|--------|
| **Default** | Match changed files to skills by pattern (see Skill Mapping below) |
| **Fallback** | `.claude/skills/*/SKILL.md` — scan all skills for applicable review guidance |
| **Override** | `CLAUDE.md` → `Audit Configuration` → `skill_mappings` |

### Skill Mapping (Default)

Map changed files to the skill that should review them. The orchestrator spawns
one agent per applicable skill per ticket (full matrix).

Auto-detect by scanning `.claude/skills/*/SKILL.md` for skills that mention relevant
file types in their description or review checklists. Then load that skill's `references/`
directory files. Only include reference files that actually exist.

**Common mappings (auto-detected from skill descriptions):**

| File Pattern | Likely Skill | How Detected |
|-------------|-------------|-------------|
| `*.cs`, `*.razor` | `dotnet-api` (if exists) | Skill description mentions ".NET" |
| `*.tsx`, `*.ts` | Frontend skill (if exists) | Skill description mentions "React" or "component" |
| `*.py` | Python skill (if exists) | Skill description mentions "Python" |
| `*.test.*`, `*.spec.*` | Same skill as source file | Match source extension |

**For each matched skill:** Identify the path to its `SKILL.md` and `references/` directory (if exists).
Skip skills whose reference directories are missing.

**Override format in `CLAUDE.md`:**

```markdown
## Audit Configuration

### Skill Mappings

| Pattern | Skill Path |
|---------|-----------|
| `*.cs` | `.claude/skills/dotnet-api` |
| `*.tsx` | `.claude/skills/frontend` |
| `Dockerfile*` | `.claude/skills/infra` |
```

---

## `/audit` (Full Pipeline)

### When to Use

- After completing one or more tickets
- Before considering work "done"
- User says "audit", "validate my work", or "check everything"

### Step 1: Identify Scope

#### 1a. Determine tickets to audit

- From arguments: `/audit PROJ-370 PROJ-371`
- From branch: parse ticket IDs from branch name (match `PROJ-\d+` pattern)
- From `all`: query JIRA or scan local `*.jira.md` for in-progress/done tickets
- Ask user if ambiguous

#### 1b. Detect mode

| Signal | Mode | Meaning |
|--------|------|---------|
| Uncommitted changes exist (`git status --porcelain` is non-empty) OR current branch differs from main | **Active** | Work in progress — files are on the current branch |
| No uncommitted changes AND on main (or tickets not found in branch diff) | **Historical** | Tickets were completed and merged previously |
| User passes `--historical` flag | **Historical** | Force historical mode |

If ambiguous, ask: "Are you auditing in-progress work on this branch, or reviewing
previously completed tickets?"

#### 1c. Identify changed files per ticket

**Active mode** (default — work in progress):

```bash
# All changes on this branch vs main (committed + uncommitted)
git diff --name-only main...HEAD
git diff --name-only          # unstaged
git diff --name-only --cached # staged
```

Combine and deduplicate. If ticket IDs appear in commit messages, further narrow
per-ticket file sets using:

```bash
git log main..HEAD --grep="PROJ-370" --name-only --pretty=format:""
```

**Historical mode** (already merged):

```bash
# Find commits for each ticket by searching commit messages
git log --all --grep="PROJ-370" --name-only --pretty=format:"" | sort -u
```

This returns the files that were touched by commits mentioning the ticket ID.
The orchestrator identifies the **current paths** of those files for review (since
the code has been merged, HEAD has the latest state).

**Important:** In historical mode, the orchestrator can still make fixes (the code
exists on the current branch at HEAD). Fixes are applied as new changes, and
subsequent re-runs use active mode detection automatically.

#### 1d. Show audit plan

```
Audit Plan
==========
Mode: Active (uncommitted changes on feature/PROJ-370-registration)
Tickets: PROJ-370, PROJ-371

Phase 1 - Acceptance Criteria:
  PROJ-370: 4 criteria from docs/roadmap/jira/phase-3-implementation.jira.md
  PROJ-371: 3 criteria from docs/roadmap/jira/phase-3-implementation.jira.md

Phase 2 - Architecture Compliance:
  5 architecture docs loaded
  12 files to review

Phase 3 - Skill Compliance:
  dotnet-api: 10 .cs files (PROJ-370: 6, PROJ-371: 4)

Proceed? [Yes / Modify scope]
```

### Step 2: Run Phase 1 — Acceptance Criteria

**For each ticket**, spawn an independent Opus subagent:

- **Agent receives:** Ticket acceptance criteria (inline) + file paths to review
- **Agent does NOT receive:** Architecture doc paths, skill paths, or other ticket context
- **Agent task:** Read the source files, evaluate each acceptance criterion as PASS or FAIL with specific evidence
- **Agent prompt:** Use template from [references/agent-prompts.md](references/agent-prompts.md) § Acceptance Criteria Agent

**Collect all reports. Evaluate holistically:**

- If all PASS → Phase 1 green. Proceed to Phase 2.
- If any FAIL → Fix issues (orchestrator makes changes, not agents), then re-run Phase 1.
- Apply [loop safeguards](#loop-safeguards) on every re-run.

### Step 3: Run Phase 2 — Architecture Compliance

**Spawn independent Opus subagents** (one per architecture doc file — e.g., if
`docs/architecture/` has 4 `.md` files, spawn 4 agents, each reviewing against one doc):

- **Agent receives:** Path to one architecture doc + file paths to review
- **Agent does NOT receive:** Acceptance criteria, skill paths, or other architecture areas
- **Agent task:** Read the architecture doc and source files, evaluate compliance per rule as PASS or FAIL
- **Agent prompt:** Use template from [references/agent-prompts.md](references/agent-prompts.md) § Architecture Agent

**Collect all reports. Evaluate holistically:**

- If all PASS → Phase 2 green. Proceed to Phase 3.
- If any FAIL → Fix issues, then re-run Phase 2.
- **Regression check:** If fixes changed code that Phase 1 validated, regress to Phase 1.
- Apply [loop safeguards](#loop-safeguards) on every re-run.

### Step 4: Run Phase 3 — Skill Compliance

**Spawn independent Opus subagents** (one per applicable skill per ticket — full matrix):

- **Agent receives:** Path to the skill's `SKILL.md` and `references/` directory + file paths to review
- **Agent does NOT receive:** Acceptance criteria, architecture docs, or other skill references
- **Agent task:** Read the skill docs and source files, evaluate compliance per skill pattern as PASS or FAIL
- **Agent prompt:** Use template from [references/agent-prompts.md](references/agent-prompts.md) § Skill Compliance Agent

**Collect all reports. Evaluate holistically:**

- If all PASS → Phase 3 green. Proceed to Phase 4.
- If any FAIL → Fix issues, then re-run Phase 3.
- **Regression check:** If fixes changed code that Phase 1 or 2 validated, regress to Phase 1.
- Apply [loop safeguards](#loop-safeguards) on every re-run.

### Step 5: Run Phase 4 — WARN Resolution & Documentation

After all FAILs are fixed and all three phases are green, enter **WARN Resolution** to categorize, document, and persist all WARN dispositions.

**Purpose:** Create a traceable record of all WARNs, decisions, and rationale. Enable future audits to reference past decisions and avoid re-flagging accepted patterns.

#### 4a. Collect All WARNs

Aggregate WARNs from all three phases:
- Phase 1 WARNs (acceptance criteria)
- Phase 2 WARNs (architecture compliance)
- Phase 3 WARNs (skill compliance)

Deduplicate if the same issue was flagged by multiple agents (same rule + same files).

#### 4b. Check for Prior Decisions

**Read** `.claude/audits/warns/by-status.yaml` (if exists).

For each WARN:
1. Search by-status.yaml for matching rule + similar files
2. If found with same category and decision:
   - Show: "This WARN was seen before as W-{ID}. Decision: {decision}. Same?"
   - If yes → skip categorization, just add reference to current audit
   - If no → proceed to categorization (situation may have changed)

#### 4c. Categorize New WARNs (Interactive)

For each new WARN, present options to the user:

**Question:** "How should I categorize this WARN?"
**Header:** "Category"
**Options:**
1. Gap — Missing feature that should exist → Create JIRA ticket
2. Sequencing — Infrastructure ready, caller in future ticket → Document dependency
3. Improvement — Code exceeded original spec → Queue doc update
4. Divergence — Valid alternative pattern → Add standing override
5. Doc Drift — Docs stale, code current → Queue doc update (low priority)

**Follow-up questions per category:**

| Category | Follow-Up |
|----------|-----------|
| Gap | "Create JIRA ticket now?" [Yes / Describe action] |
| Sequencing | "Which ticket will address this?" (e.g., PROJ-380) |
| Improvement | "Update architecture docs?" [Now / Queue / No] |
| Divergence | "Add as standing override?" [Yes / No] |
| Doc Drift | "Priority?" [High / Medium / Low] |

#### 4d. Assign WARN IDs

**Read** `.claude/audits/warns/by-status.yaml` (if exists) to find highest existing ID (or start at W-001).
Assign sequential IDs to new WARNs: W-{NNN}.

#### 4e. Write Resolution Files

**Read** [references/warn-resolution-tracking.md](references/warn-resolution-tracking.md) § Write Patterns for the full write sequence and data formats. Create/update all tracking files listed there.

#### 4f. Update CLAUDE.md (Optional)

If any standing overrides were added, offer to update `CLAUDE.md`:

```markdown
## Audit Configuration

### Standing Overrides

See `.claude/audits/overrides.yaml` for the canonical list.
This section summarizes key overrides for human reference.

| Override | Rationale |
|----------|-----------|
| Exception-based error handling | ADR-007, ExceptionHandlingMiddleware |
```

#### 4g. Create JIRA Tickets (for Gaps)

For each gap WARN where user selected "create ticket now":
- Use `/jira create` to create the ticket
- Record ticket ID in by-status.yaml and detail file
- Add ticket to roadmap markdown

#### 4h. Offer Architecture Doc Updates

For improvements/doc-drift flagged "update now":

```
Architecture Updates Needed:
============================

docs/architecture/code-contracts.md:
  - Section 6.2: Add HashCode to IBackupCodeService spec

Update now? [Yes / Queue for architecture skill / Skip]
```

If "Yes" → make edits. If "Queue" → ensure in outbox. If "Skip" → mark deferred in by-status.yaml.

### Step 6: Final Report

```
Audit Complete
==============

Phase 1 - Acceptance Criteria: PASS (2 tickets, 7/7 criteria)
  PROJ-370: 4/4 PASS
  PROJ-371: 3/3 PASS

Phase 2 - Architecture Compliance: PASS (5 rule sets, 0 violations)

Phase 3 - Skill Compliance: PASS (1 skill, 10 files)
  dotnet-api: 10/10 files compliant

Phase 4 - WARN Resolution: Complete
  Total WARNs: 7
  Categories: 2 gaps, 1 sequencing, 2 improvements, 1 divergence, 1 doc drift
  Actions: 2 JIRA tickets created, 1 standing override added, 3 doc updates queued

Fix Cycles: 1 (Phase 3 had 2 issues, fixed in first cycle)
Regressions: 0

Files Modified During Audit:
  src/Application/Features/Users/RegisterUser.cs (Phase 3 fix)
  src/Application/Features/Users/VerifyEmail.cs (Phase 3 fix)

Resolution Tracking:
  Registry: .claude/audits/warns/by-status.yaml (W-001 through W-007)
  Detail: .claude/audits/warns/by-run/2026-02-07-proj-370-371.md
  Outbox: .claude/audits/outbox/doc-updates.md (3 pending)
  Tickets: PROJ-429 (rate limiting), PROJ-430 (MFA disable)
```

---

## `/audit ac`

### When to Use

- Quick AC validation without full pipeline
- Verifying a single ticket's criteria during development
- Re-checking after fixing AC failures

### Steps

1. Run Step 1 (Identify Scope) to resolve tickets and changed files
2. Run Step 2 (Phase 1 — Acceptance Criteria)
3. Stop after green. Present Phase 1 results only.

---

## `/audit arch`

### When to Use

- Checking architecture compliance before a full audit
- After refactoring that changes project structure or layer boundaries
- Validating a PR against architecture docs only

### Steps

1. **Scope:** `git diff --name-only main...HEAD` for all changed files on this branch
2. Run Step 3 (Phase 2 — Architecture Compliance) against those files
3. Stop after green. No regression to Phase 1.

---

## `/audit skill`

### When to Use

- Reviewing code against skill patterns without AC or architecture checks
- After applying a new skill's patterns to existing code
- Quick quality check on a specific file type (e.g., `.cs` files against `dotnet-api`)

### Steps

1. **Scope:** `git diff --name-only main...HEAD` for all changed files on this branch
2. Match files to skills using the skill mapping rules
3. Run Step 4 (Phase 3 — Skill Compliance) with one agent per applicable skill
4. Stop after green. No regression to earlier phases.

---

## Loop Safeguards

**Read** [references/loop-safeguards.md](references/loop-safeguards.md) for detection rules, conflict report format, and resolution flow.

**Key limits:** 3 fix cycles per phase, 7 total cycles, STOP on contradictory feedback or churn (same file modified 3+ times). When a loop is detected, compile a conflict report and ask the user for direction.

---

## Agent Orchestration Rules

**Read** [references/agent-prompts.md](references/agent-prompts.md) for full agent prompt templates and isolation principles.

**Key patterns:**
- Agents are **read-only reviewers** (produce reports, never fix code)
- Spawn agents in **parallel** per phase using `Task` tool with `model: "opus"`
- Use **path-based delegation** (agents read files themselves, not pre-loaded by orchestrator)
- **Isolation:** Each agent type receives only what it needs (AC agents don't see architecture rules, etc.)
- Split agents if reviewing >40 files
- WARNs collected after phase completes → user decides disposition

## Checklist

Before completing an audit run:

- [ ] All tickets identified and acceptance criteria loaded
- [ ] Changed files mapped to tickets
- [ ] Phase 1 (AC) fully green — all criteria PASS
- [ ] Phase 2 (Architecture) fully green — all rules PASS
- [ ] Phase 3 (Skill) fully green — all patterns PASS
- [ ] Phase 4 (WARN Resolution) complete — all WARNs categorized
- [ ] Resolution files written (by-run, by-status, agent-context)
- [ ] JIRA tickets created for gaps (if applicable)
- [ ] Standing overrides updated for divergences (if applicable)
- [ ] No unresolved conflicts or loop detections
- [ ] Regression checks passed after all fixes
- [ ] Final report presented with file modification list
- [ ] No fixes introduced new issues (all phases still green)

## Reference Files

- **Agent prompts**: [references/agent-prompts.md](references/agent-prompts.md) - Templates for each agent type
- **Loop safeguards**: [references/loop-safeguards.md](references/loop-safeguards.md) - Detection rules and conflict reports
- **WARN tracking**: [references/warn-resolution-tracking.md](references/warn-resolution-tracking.md) - Resolution system design
- **Project conventions**: `CLAUDE.md` (if exists)
- **Ticket data**: `docs/roadmap/jira/*.jira.md` (default) or JIRA API
- **Architecture docs**: `docs/architecture/*.md` (default, if exists)
- **Skill references**: `.claude/skills/*/SKILL.md` and their `references/` directories (if exists)
- **Audit tracking**: `.claude/audits/` - Resolution files, overrides, outbox

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/acampb) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
