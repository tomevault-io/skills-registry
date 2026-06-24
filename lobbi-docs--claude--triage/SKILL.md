---
name: jira-issue-triage-and-routing
description: This skill should be used when the user asks to "triage issue", "classify ticket", "route jira", "analyze priority", "categorize issue", "determine complexity", "route to agents", or needs guidance on classifying, prioritizing, and routing Jira issues to appropriate agents and workflows. Use when this capability is needed.
metadata:
  author: lobbi-docs
---

# Jira Issue Triage and Routing Skill

Intelligent classification, prioritization, and routing system for Jira issues.

## Triage Decision Tree

```
START: New Jira Issue
├─ Step 1: ISSUE TYPE CLASSIFICATION
│  ├─ Bug? → Classify severity (Blocker/Critical/Major/Minor) → HIGH Priority if Critical
│  ├─ Story/Feature? → Check requirements clarity, sprint scope, dependencies
│  ├─ Epic? → Route to epic-decomposer (NEVER implement directly)
│  ├─ Task? → Route by category (Tech Debt/Config/Docs/Infrastructure)
│  └─ Spike? → Time-box (1-2 days max) → Document findings → Create stories
│
├─ Step 2: COMPLEXITY ASSESSMENT (0-100 scale)
│  ├─ Code Impact: Single file (2) → Multiple services (10)
│  ├─ Integration: None (0) → Multiple external APIs (10)
│  ├─ Risk: None (0) → Critical/Data loss (10)
│  ├─ Testing: No tests (0) → Complex E2E scenarios (10)
│  ├─ Dependencies: None (0) → Multiple blocking (10)
│  └─ Uncertainty: Known (0) → Complete unknown (10)
│
│  Scoring: (Code×0.25 + Integration×0.20 + Risk×0.20 + Testing×0.15 + Dependencies×0.10 + Uncertainty×0.10) × 10
│
│  Categories:
│  • 1-20: SIMPLE → Quick-Fix Path (2-3 agents, 2-4 hrs)
│  • 21-40: MODERATE → Standard Workflow (3-5 agents, 2-5 days)
│  • 41-70: COMPLEX → Extended Workflow (5-10 agents, 5-10 days)
│  • 71+: VERY COMPLEX → Decomposition Path
│
├─ Step 3: PRIORITY & SEVERITY ASSESSMENT
│  ├─ Business Impact: Blocks production (BLOCKER) → Nice-to-have (LOW)
│  ├─ Urgency: Immediate (hours) → Backlog (months)
│  └─ Bug Severity Matrix:
│     • BLOCKER: Production down, data loss, security breach (1-4 hr SLA)
│     • CRITICAL: Major functionality broken (4-8 hr SLA)
│     • MAJOR: Important feature degraded (1-3 days)
│     • MINOR: Cosmetic issues (next sprint)
│
├─ Step 4: WORKFLOW ROUTING
│  ├─ QUICK-FIX: Simple (1-20), <50 LOC, low risk → EXPLORE→CODE→TEST→COMMIT
│  ├─ STANDARD: Moderate (21-40) → EXPLORE→PLAN→CODE→TEST→FIX→COMMIT
│  ├─ EXTENDED: Complex (41-70), high risk → All phases + extended thinking + checkpoints
│  ├─ RESEARCH: Spike/POC → RESEARCH→DOCUMENT→CREATE STORIES (time-boxed)
│  └─ DECOMPOSITION: Epic (71+) → ANALYZE→DECOMPOSE→CREATE STORIES→TRIAGE EACH
│
├─ Step 5: AGENT SELECTION
│  ├─ By Type: Bug→triage-agent; Story→requirements-analyzer; Epic→epic-decomposer; Spike→requirements-analyzer
│  ├─ By Tech: Frontend→react-specialist; Backend→nodejs/python/java-specialist; DevOps→k8s-specialist
│  ├─ By Phase: EXPLORE→requirements-analyzer; PLAN→architect; CODE→tech-specific; TEST→test-strategist; COMMIT→commit-orchestrator
│  └─ By Complexity: SIMPLE→2-3 junior agents; COMPLEX→5-10 seniors + extended thinking
│
├─ Step 6: RISK & ESCALATION
│  ├─ Risk Factors: Security, breaking changes, data migrations, compliance issues
│  ├─ Level 1 (IMMEDIATE STOP): Security vulnerability, data loss, compliance breach
│  ├─ Level 2 (CHECKPOINT): Complexity +50%, blocker >4hrs, breaking changes
│  └─ Level 3 (POST-COMPLETION): Standard bugs, docs, minor refactoring
│
└─ Step 7: OUTPUT ROUTING PACKAGE
   ├─ Classification, complexity score, priority, workflow path
   ├─ Agent selection, risk assessment, escalation triggers
   └─ Update Jira labels, assign workflow, spawn agents
```

## Issue Type Routing Matrix

| Type | Detection | Routing | Agents |
|------|-----------|---------|--------|
| **Bug** | Title: "bug", "broken", "error"; Stack trace present | Route by severity + "Can reproduce?" check | triage-agent, hypothesis-debugger, root-cause-analyzer |
| **Story** | "Add", "implement", "create"; User story format | Check requirements, sprint scope, decompose if >13 pts | requirements-analyzer, requirements-analyzer, task-enricher |
| **Epic** | Type=Epic; Multi-sprint scope | DECOMPOSE into 3-8 stories (Foundation→Core→Enhancement→Polish phases) | epic-decomposer, strategic-planner |
| **Task** | No user-facing change | Tech Debt→code-quality; Config→devops; Docs→doc-writer | domain-specialists |
| **Spike** | "Investigate", "research", "POC"; Unknown outcome | Time-box 1-2 days, document findings, create stories | requirements-analyzer, requirements-analyzer |

## Complexity Scoring Example

```
Issue: Add CSV export functionality

Code Impact: 5 (3-4 files, 200-300 LOC) × 0.25 = 1.25
Integration: 3 (internal APIs) × 0.20 = 0.60
Risk: 4 (file generation, perf) × 0.20 = 0.80
Testing: 6 (unit + integration) × 0.15 = 0.90
Dependencies: 2 (DB query opt) × 0.10 = 0.20
Uncertainty: 3 (format details) × 0.10 = 0.30

Score: 3.65 × 10 = 36.5 → MODERATE (5 story points)
→ Standard Workflow, 3-5 agents, 2-5 days
```

## Epic Decomposition Example

```
Epic: User Management System

Phase 1 (Sprint 1): PROJ-101 Profile CRUD (5pts), PROJ-102 RBAC (8pts), PROJ-103 Password Policy (3pts)
Phase 2 (Sprint 2): PROJ-104 Audit Logging (5pts), PROJ-105 MFA (8pts)
Phase 3 (Sprint 3): PROJ-106 Bulk Ops (5pts), PROJ-107 Import/Export (5pts)
Phase 4 (Sprint 4): PROJ-108 Analytics (8pts)

Dependencies: PROJ-102 blocks PROJ-106; PROJ-101 blocks PROJ-104
Total: 8 stories, 47 points, 4 sprints, 2-3 developers
```

## Workflow Paths

| Path | Criteria | Phases | Agents | Duration |
|------|----------|--------|--------|----------|
| **Quick-Fix** | Complexity 1-20, <50 LOC, low risk | EXPLORE→CODE→TEST→COMMIT | 2-3 | 2-4 hrs |
| **Standard** | Complexity 21-40, moderate risk | EXPLORE→PLAN→CODE→TEST→FIX→COMMIT | 3-5 | 2-5 days |
| **Extended** | Complexity 41-70, high risk | All phases + extended thinking + checkpoints | 5-13 | 5-10 days |
| **Research** | Spike/POC, time-boxed | RESEARCH→DOCUMENT→CREATE STORIES | 1-3 | 1-5 days |
| **Decomposition** | Epic, complexity 71+ | ANALYZE→DECOMPOSE→CREATE STORIES→TRIAGE | 2-4 | 1-2 days |

## Agent Selection by Phase

| Phase | SIMPLE | MODERATE | COMPLEX | VERY COMPLEX |
|-------|--------|----------|---------|--------------|
| EXPLORE | requirements-analyzer (1) | requirements-analyzer, requirements-analyzer (2) | requirements-analyzer, dep-mapper, architect (3) | architect, senior-analyst (3) |
| PLAN | — | requirements-analyzer (1) | requirements-analyzer, code-architect (2) | senior-architect (2) |
| CODE | junior-dev (1-2) | task-enricher (2-4) | task-enricher (3-6) | senior-specialists (4-6) |
| TEST | test-runner (1) | test-strategist (1-2) | test-strategist, qa-ticket-reviewer (2-3) | comprehensive-tester (2-4) |
| FIX | — | hypothesis-debugger (1) | hypothesis-debugger, code-quality-enforcer (1-2) | senior-debugger (1-2) |
| COMMIT | commit-orchestrator (1) | commit-orchestrator (1) | commit-orchestrator, documentation-hub (1-2) | commit-orchestrator, documentation-hub (1-2) |

## Escalation Matrix

| Trigger | Level | Action | Timeline | Notify |
|---------|-------|--------|----------|--------|
| Security vulnerability | 1 IMMEDIATE | STOP work, create incident | Immediate | Security, management |
| Data loss risk | 1 IMMEDIATE | STOP work, document | Immediate | Ops, management |
| Complexity +50% | 2 CHECKPOINT | Pause, request decision | Same day | Tech lead, product owner |
| Blocker >4 hrs | 2 CHECKPOINT | Escalate blocker | Within 4 hrs | Blocking team, manager |
| Breaking change | 2 CHECKPOINT | Document impact, approve | 1-2 days | Affected teams |
| Standard bug fix | 3 POST-COMPLETION | Complete, notify | After done | Reviewer |

## Bug Routing Decision

```
Bug Detected
├─ Can reproduce consistently? NO → Gather reproduction steps
├─ Security vulnerability? YES → IMMEDIATE ESCALATION
├─ Severity?
│  ├─ BLOCKER/CRITICAL → Extended workflow, root-cause analysis, human checkpoints
│  ├─ MAJOR → Standard workflow, add regression tests
│  └─ MINOR → Quick-Fix path, batch with similar fixes
└─ Root cause known? NO → Extend EXPLORE phase with hypothesis-debugger/profiler
```

## Feature Routing Decision

```
Story/Feature Detected
├─ Requirements clear? NO → Route to requirements-analyzer first
├─ Single sprint scope? NO → Consider epic decomposition
├─ Tech complexity?
│  ├─ Frontend → UI specialists
│  ├─ Backend → API specialists
│  ├─ Full-stack → Both
│  └─ Infrastructure → DevOps specialists
├─ Integration complexity?
│  ├─ None → SIMPLE (1-20 pts)
│  ├─ Internal APIs → MODERATE (21-40 pts)
│  ├─ External APIs → COMPLEX (41-70 pts)
│  └─ Multiple external → VERY COMPLEX (71+ pts)
└─ Score 1-100 and route to appropriate workflow
```

## Spike Handling

```
Spike Detected
├─ Time-Box Definition (CRITICAL)
│  ├─ Small: 1 day max
│  ├─ Medium: 2-3 days max
│  └─ Large: 1 week max (justify)
├─ Research Phase: Investigate tech/approach, build POC if needed
├─ Document Phase: Write findings, recommend approach, estimate effort
└─ Output: Go/No-Go decision OR implementation stories with estimates
```

## Triage Output Checklist

- [ ] Issue classification (type, subtype, confidence)
- [ ] Complexity score (0-100) with factor breakdown
- [ ] Priority & severity assessment
- [ ] Workflow path selection (Quick-Fix/Standard/Extended/Research/Decomposition)
- [ ] Agent recommendations per phase
- [ ] Risk assessment and escalation level
- [ ] Dependencies and blockers
- [ ] Jira labels and field updates
- [ ] Execute workflow with selected agents

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/lobbi-docs) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
