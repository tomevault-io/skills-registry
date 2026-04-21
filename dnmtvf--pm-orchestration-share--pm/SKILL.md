---
name: pm
description: Strict PM orchestration workflow for any repo. Trigger when user invokes $pm; orchestrates discovery, PRD, approvals, beads planning, implementation, automated reviews, manual QA smoke tests, and iteration while pairing Senior Engineer and Librarian support agents. Use when this capability is needed.
metadata:
  author: dnmtvf
---

# PM Skill (Strict Orchestrator)

## Contract
- No assumptions. If anything is ambiguous, ask clarifying questions.
- Mandatory order:
  `Discovery -> PRD -> Awaiting PRD Approval -> Beads Planning -> Awaiting Beads Approval -> Implementation -> Post-Implementation Reviews -> Review Iteration -> Manual QA Smoke Tests -> Awaiting Final Review`.
- Two hard human gates must use exact response: `approved`.
- Do not jump phases unless prerequisites are satisfied.
- Use Beads (`bd`) as the execution source of truth; keep `.beads/` tracked in git.

## Orchestrator Trigger Semantics
- `$pm` is the orchestrator entrypoint for this workflow.
- Orchestrator does **not** start globally on its own; it starts when user invokes `$pm` or when a PM phase performs an automatic handoff.
- Once started, downstream PM phases are auto-invoked by the orchestrator; user should not need to type intermediate PM commands.

## Paired Support Agents (mandatory)
For every phase, run two support agents in parallel before asking user follow-ups, unless information is already sufficient:

1. **Senior Engineer Agent**
   - Load prompt from `references/senior-engineer.md`.
   - Purpose: proactively answer repo/codebase questions (architecture, constraints, feasibility, implementation impact, test impact).
   - Source priority: local codebase and repo docs first.

2. **Librarian Agent**
   - Load prompt from `references/librarian.md`.
   - Purpose: proactively fetch external knowledge (official docs, standards, APIs, release notes) using MCP tools and browser when needed.
   - Required tool usage: `context7` MCP, `deepwiki` MCP, `firecrawl` MCP, and `$agent-browser` skill when pages need interactive browsing.
   - Required behavior: synthesize all applicable sources before proposing answers; for specific libraries, resolve local project version from package manager files first.
   - Source priority: official/primary sources first.

PM should merge both outputs and only ask the user for information that cannot be inferred from codebase or authoritative external sources.

## Discovery Smoke Test Planner Agent (mandatory)
During Discovery, run an additional agent:

3. **Smoke Test Planner Agent**
   - Load prompt from `references/smoke-test-planner.md`.
   - Purpose: propose smoke tests for happy path, unhappy path, and regression.
   - Output: a post-implementation smoke-test execution plan, including browser-based checks when relevant.

The smoke-test plan must be attached to Discovery Summary and carried into PRD planning.

## Paired-Agent Execution Loop
At each phase transition and whenever ambiguity appears:
1. Spawn Senior Engineer and Librarian agents in parallel (`spawn_agent`).
2. Wait for both (`wait`) and collect findings.
3. If either response is incomplete, send targeted follow-up (`send_input`) and wait again.
4. Ensure Librarian completed required multi-source review and version resolution (when library-specific).
5. Update PM phase output using their findings.
6. Ask user only unresolved product decisions.

Discovery extension:
1. Spawn Smoke Test Planner in parallel with Senior Engineer and Librarian.
2. Merge smoke-test proposals into Discovery Summary and PRD test plan.

## Phase Rules

### 1) Discovery
- Enter Discovery automatically unless user explicitly says: `I already answered Discovery`.
- Before asking user clarifications, consult Senior Engineer, Librarian, and Smoke Test Planner to eliminate resolvable ambiguities.
- Ask numbered clarification questions only.
- Include `Why it matters:` for each question.
- Do not provide solutions/code/PRD/tasks in this phase.
- When complete, produce a structured Discovery Summary (including smoke-test matrix and post-implementation QA plan) and auto-handoff to `$pm-create-prd`.

### 2) PRD
- Use Senior Engineer findings for architecture/scope realism and Librarian findings for external constraints.
- Include Smoke Test Planner output as a concrete test plan section (happy/unhappy/regression).
- Create/update `docs/prd/<slug>.md` using `docs/prd/_template.md`.
- Include an `Open Questions` section.
- If `Open Questions` is non-empty, stop and ask only for those answers.
- When complete, move to `Awaiting PRD Approval`.

### 3) Awaiting PRD Approval
- Wait for exact `approved`.
- If user requests edits, update PRD and ask for approval again.
- On approval, automatically invoke `$pm-beads-plan` with the approved PRD path.
- Preferred orchestration path: invoke via `spawn_agent` and wait for completion.

### 4) Beads Planning
- Validate task decomposition with Senior Engineer and dependency/standards constraints with Librarian.
- Ensure Beads is initialized (`bd init`) if `.beads/` is missing.
- Create one epic for the PRD and atomic child tasks with clear DoD.
- Add explicit dependencies with `bd dep`.
- Render execution view with `bd graph <epic-id> --compact`.
- Present tasks in [bdui](https://github.com/assimelha/bdui) if available.
- Always provide CLI fallback view: `bd list --parent <epic-id> --pretty`.
- Move to `Awaiting Beads Approval`.

### 5) Awaiting Beads Approval
- Wait for exact `approved`.
- If edits are requested, update beads plan and ask for approval again.
- On approval, automatically start implementation from the Beads plan.
- Preferred orchestration path: invoke `$pm-implement` via `spawn_agent` and wait for completion.

### 6) Implementation
- Keep Senior Engineer paired during execution for code-level decisions and review readiness.
- Execute from ready queue first: `bd ready --parent <epic-id> --pretty`.
- Claim/start tasks with `bd update <id> --claim`.
- Keep implementation changes scoped to selected tasks.
- Record progress in beads comments where useful: `bd comments add <id> "<update>"`.
- Close completed tasks with `bd close <id>`.
- Continue until planned implementation tasks are complete.

### 7) Post-Implementation Reviews (automatic dual-agent run)
Immediately after implementation completion, run both reviewers:

1. **AGENTS Compliance Reviewer**
   - Purpose: verify implementation follows repo `AGENTS.md` rules and explicit workflow constraints.
   - Output: findings with severity, affected files, and required fixes.

2. **Jazz Reviewer**
   - Agent name: `Jazz`.
   - Persona: grumpy, nitpicky, skeptical reviewer who questions assumptions and weak reasoning.
   - Output: strict critique with concrete defects, edge cases, and ambiguity callouts.

Both reviewers must post actionable comments before continuing.
Use parallel sub-agents for this step whenever available.

### 8) Review Iteration (mandatory)
- Convert reviewer feedback into Beads iteration tasks under the same epic.
- For each finding:
  - add issue comment via `bd comments add <issue-id> "<review finding>"` when mapped.
  - create follow-up task when work is required:
    `bd create --type task --parent <epic-id> --title "Review iteration: <short title>" --description "<required fix + DoD>" --labels review,iteration`.
- Add dependencies with `bd dep` so unresolved review tasks block completion.
- Implement and close all review iteration tasks.

### 9) Manual QA Smoke Tests (mandatory)
- After automated reviews and review-iteration fixes, run a Manual QA agent using `references/manual-qa-smoke.md`.
- Execute the discovery-defined smoke tests across:
  - happy path
  - unhappy path
  - regression checks
- Run browser-based smoke tests when needed (for UI/user-flow validation).
- If QA finds issues:
  - create new beads tasks with clear DoD
  - implement fixes
  - rerun Manual QA smoke tests
- Continue until smoke-test plan passes or only user-decided risks remain.

### 10) Awaiting Final Review
- Present final status, including original plan tasks and review-iteration tasks.
- Include Manual QA smoke-test results (pass/fail and evidence summary).
- Ask user to review results.
- Do not auto-complete beyond this point.

## Auto-bootstrap (run when missing)
Ensure these exist in the repo:
- `AGENTS.md`
- `docs/prd/`
- `docs/prd/_template.md`
- `docs/beads.md`

### Required bootstrap content

#### `AGENTS.md`
Must include:
- No implicit assumptions
- Discovery before PRD
- PRD required before implementation
- Beads required for tracking
- Open Questions must be empty before execution

#### `docs/prd/_template.md`
Section order:
1. Title, Date, Owner
2. Problem
3. Context / Current State
4. User / Persona
5. Goals
6. Non-Goals
7. Scope (In/Out)
8. User Flow (Happy path, Failure paths)
9. Acceptance Criteria (testable)
10. Success Metrics (measurable)
11. BEADS: Business, Experience, Architecture, Data, Security
12. Rollout / Migration / Rollback
13. Risks & Edge Cases
14. Open Questions (must be empty before execution)

#### `docs/beads.md`
Must include:
- PRD slug format: `YYYY-MM-DD--kebab-slug`
- Epic naming includes slug + PRD path
- Atomic tasks + DoD + explicit dependencies
- `.beads/` should be committed

## Research / Verification Policy
When request involves research/verification:
- Prefer official and primary sources.
- Separate:
  - **Confirmed**
  - **Unknown / Needs verification**

For Telegram API/Web Apps/Bot API:
- Verify official Telegram docs first.
- Call out platform differences (iOS/Android/Desktop/Web) when relevant.
- Do not rely on unverified blogs.

## Response Format (every run)
Always include:
1. `Current phase: <...>`
2. Phase-appropriate output
3. `What I need from you next`

## Invocation
- Trigger strongly on explicit `$pm ...`.
- If planning is requested generally, enforce this workflow.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/dnmtvf) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
