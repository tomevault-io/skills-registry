---
name: plan
description: Orchestrate the planning workflow (Idea → Plan → VD → WO → TASK/FIX) for SUM Platform releases and infrastructure initiatives. Use when starting a new version or initiative, creating planning artifacts, or generating GitHub issues from planning markdown. Follows PROJECT-PLANNING-GUIDELINES.md and creates deterministic, AI-executable TASK tickets. Use when this capability is needed.
metadata:
  author: markashton480
---

# Planning Skill

> **Orchestrate version planning for SUM Platform releases and infrastructure initiatives.**
>
> This skill guides you through the complete planning workflow from initial idea to GitHub issues, ensuring all planning artifacts follow PROJECT-PLANNING-GUIDELINES.md and creating AI-executable TASK tickets.

---

## When to Use This Skill

### Explicit Triggers (User Invokes)

- `/plan` or `$plan` — Direct skill invocation
- "Plan version X.Y.Z" — Release planning request
- "Plan initiative <name>" — Infrastructure planning request
- "Create planning for..." — Explicit planning workflow request

### Implicit Triggers (Auto-Activate)

The skill should auto-activate when the user's prompt matches these patterns:

- "Start planning" + version number
- "It's time to start planning the next version"
- "We need to plan WOs"
- "Use the planning skill"
- "Plan <WO description>" when no VD context exists
- "Break this into tasks" when planning context exists
- "Create VD/WO/TASK issues" with planning artifacts present

### Non-Triggers (Do NOT Use)

- "Start a release" — Ambiguous (planning vs execution); ask for clarification
- "Execute issue #NNN" — Use GH-ISSUE-PROMPT skill (task execution, not planning)
- "Review PR #NNN" — Different workflow
- Code implementation requests — Not a planning concern

**Disambiguation:** If user says "start a release" without "planning", ASK: "Do you want to (A) plan the release (create VD/WO/TASK), or (B) execute the release (run RELEASE_AGENT_PROMPT)?"

---

## Planning Workflow Overview

This skill implements a 7-stage workflow mapped to `docs/dev/planning/PROJECT-PLANNING-GUIDELINES.md`:

```
Stage 1: Version Identification
    ├─ Determine flow type: release/* or infra/*
    ├─ Gather required inputs
    └─ Verify/create parent VD branch

Stage 2: Author Planning Markdown on docs/planning-* (SSOT)
    ├─ Create docs/planning-* branch from VD branch
    ├─ Write planning files (VD/IMPLEMENTATION_PLAN/WO/TASK)
    ├─ Run RCI quality gate for all TASKs
    ├─ Commit planning markdown
    └─ Open DRAFT PR with Bypass Justification

Stage 3: Review Planning PR (Draft)
    ├─ Verify all planning files exist
    ├─ Verify Branch tables populated
    ├─ Verify RCI passed for all TASKs
    └─ Keep PR in draft until approved

Stage 4: Create GitHub Issues (after approval, PR still draft)
    ├─ Create VD issue
    ├─ Create WO issues (with component labels)
    ├─ Create TASK issues (with component labels)
    ├─ Link hierarchy: gh sub-issue add
    └─ Assign to project board (manual via UI)

Stage 5: Backfill Issue Numbers + Sync Bodies + Mark PR Ready
    ├─ Build Issue Index in IMPLEMENTATION_PLAN.md
    ├─ Replace #TBD with real issue numbers
    ├─ Commit backfill changes on docs/planning-* branch
    ├─ Sync ALL issue bodies: gh issue edit --body-file
    └─ Mark PR ready for review

Stage 6: Merge Planning PR + Final Verification
    ├─ Merge planning PR into VD branch
    ├─ Verify zero #TBD remaining
    ├─ Verify sub-issue hierarchy matches
    ├─ Verify Branch tables accurate
    └─ Verify issue bodies match planning files

Stage 7: Execution Handoff (Feature Branch Pre-Create)
    ├─ PM/admin pre-creates feature/* branches before first task
    └─ Tasks branch from feature/* per Branch tables
```

**Key Principle:** Planning markdown is authored on a `docs/planning-*` branch, reviewed and approved before issues are created, then backfilled and merged. Planning markdown is the SSOT; issue bodies are copies.

---

## Stage 1: Version Identification

### Required Inputs

**Stop gate:** Cannot proceed without all required inputs.

| Input | Required | When | Stop Gate |
|-------|----------|------|-----------|
| Flow type | Yes | Always | Ask if ambiguous: "release or infra?" |
| Version number | Yes (if `release/*`) | Release planning | Ask if missing |
| Initiative name | Yes (if `infra/*`) | Infrastructure planning | Ask if missing |
| Parent VD branch | Check | Always | Create if missing |

### Flow Decision

**Release planning (`release/*`):**
- Product features that ship in sum-core
- Bumps `sum-core` version
- Tagged releases consumed by client projects

**Infrastructure planning (`infra/*`):**
- Tooling, themes, CI/CD, test harnesses
- No version bump
- Internal improvements only

**Commands:**

```bash
# Release planning
git checkout develop
git pull origin develop
git checkout -b release/0.8.0
git push -u origin release/0.8.0

# Infrastructure planning
git checkout develop
git pull origin develop
git checkout -b infra/scale-infrastructure
git push -u origin infra/scale-infrastructure
```

### Planning Depth Decision (Release Only)

**Path A (Rigorous)** — Use when ANY apply:
- Major or minor version bump (X.y.z or x.Y.z)
- Multiple Work Orders expected
- Multiple components affected
- Architecture decisions required
- Estimated timeline > 1 week
- Ambiguous scope or cross-team coordination needed

**Path B (Lightweight)** — Use ONLY when ALL true:
- Patch version bump only (x.y.Z)
- Single component affected
- No breaking changes to public API
- Estimated timeline < 1 week
- Single WO with 1-3 tasks

**Upgrade rule:** If Path B reveals unexpected complexity, upgrade to Path A BEFORE creating GitHub issues.

**Default:** When in doubt, use Path A.

**Infrastructure planning:** Always uses dynamic planning (similar to Path A).

---

## Stage 2: Author Planning Markdown on docs/planning-*

**Rule:** Planning markdown is authored on a `docs/planning-*` branch and reviewed in a draft PR before issue creation.

### Directory Structure

**For releases:**
```
planning/releases/<version>/
├── VD.md
├── IMPLEMENTATION_PLAN.md
└── WO/
    ├── <work-order-slug>.md
    └── <work-order-slug>/
        └── TASK/
            └── <task-slug>.md
```

**For infrastructure:**
```
planning/infra/<initiative>/
├── VD.md
├── IMPLEMENTATION_PLAN.md
└── WO/
    ├── <work-order-slug>.md
    └── <work-order-slug>/
        └── TASK/
            └── <task-slug>.md
```

### File Content Requirements

**VD.md:**
- Copy content from `.github/ISSUE_TEMPLATE/version-declaration-template.md` (exclude YAML frontmatter)
- Replace all template placeholders with actual values
- Use `#TBD` for issue numbers (not yet created)
- See `references/template-field-checklist.md` for complete field list

**IMPLEMENTATION_PLAN.md:**
- Work breakdown (WO summary table)
- Dependency chain (if applicable)
- Issue Index (scaffolding, populated in Stage 5)
- Plan Change Log (empty table initially, updated as scope changes)

**WO/<slug>.md:**
- Copy content from `.github/ISSUE_TEMPLATE/work-order-template.md` (exclude YAML frontmatter)
- Include WO Code (2-6 uppercase chars)
- Use `#TBD` for parent VD issue number
- See `references/template-field-checklist.md` for complete field list

**TASK/<slug>.md:**
- Copy content from `.github/ISSUE_TEMPLATE/task-template.md` (exclude YAML frontmatter)
- Use `#TBD` for parent WO issue number
- **MUST pass RCI quality gate** (see below)
- See `references/template-field-checklist.md` for complete field list

### RCI Quality Gate (TASK Writing)

**Purpose:** Ensure every TASK is AI-executable (not "human nice").

**Process:**
1. Draft the TASK using task-template.md structure
2. Critique against failure modes (see `references/rci-critique-prompts.md`)
3. Revise and iterate until all stop gate questions pass

**Stop gate questions (all must be "yes"):**
- [ ] Can an agent create the branch without asking questions?
- [ ] Are acceptance criteria checkable (yes/no, not subjective)?
- [ ] Is the change surface explicit (files/paths listed)?
- [ ] Does test strategy explain what "passing" means?
- [ ] Are all constraints stated in the ticket (not just linked)?
- [ ] Does the ticket include an "Assumptions & Decision Policy" section?

**If any answer is "no":** Revise the TASK before moving to Stage 3.

**Full RCI details:** See `references/rci-critique-prompts.md`

### Commit Planning Markdown + Open Draft PR

```bash
# Release planning
git checkout release/<version>
git pull origin release/<version>
git checkout -b docs/planning-<version>

git add planning/releases/<version>/
git commit -m "chore(plan): initialize v<version> planning"
git push -u origin docs/planning-<version>

gh pr create --base release/<version> --head docs/planning-<version> --draft \
  --title "docs(planning): initialize v<version> plan" \
  --body "$(cat <<'EOF'
## Summary
- Initialize planning docs for v<version>

## Testing
- N/A (planning docs only)

## Bypass Justification
Planning docs must be reviewed and approved before issue creation.
EOF
)"

# Infrastructure planning
git checkout infra/<initiative>
git pull origin infra/<initiative>
git checkout -b docs/planning-<initiative>

git add planning/infra/<initiative>/
git commit -m "chore(plan): initialize <initiative> planning"
git push -u origin docs/planning-<initiative>

gh pr create --base infra/<initiative> --head docs/planning-<initiative> --draft \
  --title "docs(planning): initialize <initiative> plan" \
  --body "$(cat <<'EOF'
## Summary
- Initialize planning docs for <initiative>

## Testing
- N/A (planning docs only)

## Bypass Justification
Planning docs must be reviewed and approved before issue creation.
EOF
)"
```

---

## Stage 3: Review Planning PR (Draft)

**Checklist:**
- [ ] All planning files exist (VD, IMPLEMENTATION_PLAN, WOs, TASKs)
- [ ] Branch tables are populated (no placeholders like `<parent-branch>`)
- [ ] RCI quality gate passed for all TASKs (all stop gate questions = "yes")
- [ ] Planning PR is approved but remains **draft** until issue creation/backfill

**Stop gate:** If any checkbox unchecked, return to Stage 2 and fix before proceeding.

---

## Stage 4: Create GitHub Issues (After PR Approval)

**Prerequisites:**
- Draft planning PR approved (PR remains draft)
- You are on the `docs/planning-*` branch with the planning files

### Create-Then-Link Flow

**Important:** `gh sub-issue create` does not support `--body-file`. Use two-step flow: create issue, then link to parent.

**Full command blocks:** See `references/github-issue-commands.md` for copy/paste-ready commands for both release and infra flows.

### Summary (both flows)

1. Create VD issue: `gh issue create --body-file VD.md` (no component label)
2. Create WO issues: `gh issue create --body-file WO/<slug>.md` (with component label)
3. Create TASK issues: `gh issue create --body-file TASK/<slug>.md` (with component label)
4. Link hierarchy: `gh sub-issue add <parent> <child>` (VD→WO, WO→TASK)
5. Manually assign to project board via GitHub UI

### VD Title Conventions

**Release:** `VD: v<version> - <title>` (with leading `v`)
**Infrastructure:** `VD: <Initiative Title>` (no version prefix)

### Component Labels (Taxonomy)

**Valid values:** `component:core`, `component:cli`, `component:boilerplate`, `component:themes`, `component:docs`

**Rule:** VD issues do NOT require component labels. WO and TASK issues MUST have exactly one.

---

## Stage 5: Backfill Issue Numbers + Sync Bodies + Mark PR Ready

**Full command blocks:** See `references/github-issue-commands.md` for copy/paste-ready commands for both release and infra flows.

### Summary

1. **Build Issue Index** in `IMPLEMENTATION_PLAN.md` (table: Type | # | Planning File)
2. **Replace `#TBD` placeholders** with real issue numbers in all planning markdown
3. **Commit backfill (on `docs/planning-*`):** `chore(plan): link v<version> issues` (or `link <initiative> issues`)
4. **Sync ALL issue bodies:** `gh issue edit <num> --body-file <file>.md` for each issue
5. **Mark PR ready:** `gh pr ready <PR_NUMBER>`

---

## Stage 6: Merge Planning PR + Final Verification

**Merge step:**
- Merge the planning PR into the VD branch after it is marked ready.

**Checklist:**
- [ ] Zero `#TBD` remaining: `grep -r "#TBD" planning/` returns nothing
- [ ] GitHub sub-issue hierarchy verified via GitHub UI (VD→WO, WO→TASK)
- [ ] Branch tables accurate and match VD branch reality
- [ ] Issue bodies match planning files (Contract 5 check for each issue)

**Contract 5 check (per issue):**

```bash
ISSUE=123
FILE=planning/releases/0.8.0/WO/dynamic-forms/TASK/add-form-serializer.md

gh issue view "$ISSUE" --json body -q .body > /tmp/issue-body.md
diff -u "$FILE" /tmp/issue-body.md && echo "✓ In sync" || echo "✗ MISMATCH"
```

**If mismatch found:** STOP, reconcile planning file and issue body, then resume.

**Optional verification script (run from repo root):**

```bash
~/.claude/skills/plan/scripts/verify-planning-tree.sh planning/releases/0.8.0
```

---

## Stage 7: Execution Handoff (Feature Branch Pre-Create)

- PM/admin pre-creates `feature/<work-order-slug>` from the VD branch before the first task starts.
- Tasks always branch from `feature/<work-order-slug>` per the Branch table.
- Do not use "first task PR creates feature branch" bootstrap; it is blocked by PR-only rulesets.

---

## Output Contracts

### Contract 1: Planning Markdown is SSOT

Planning markdown files are the single source of truth. GitHub issue bodies are created by copying planning markdown. If an issue body must change, update planning markdown first, then sync to GitHub.

### Contract 2: Template Content Only (No YAML)

When creating planning files, copy content from `.github/ISSUE_TEMPLATE/*.md` but exclude YAML frontmatter.

### Contract 3: Issue Number Backfill

After creating each GitHub issue, update planning markdown with actual issue number (replace `#TBD`), commit on the `docs/planning-*` branch, then sync issue bodies.

### Contract 4: Branch Table Accuracy

Every planning file's Branch table must be copy/paste ready (no placeholders), verified against `docs/dev/GIT_STRATEGY.md`.

### Contract 5: Mismatch Stop Gate

Before starting any TASK/FIX, verify planning file matches issue body using diff command. STOP if mismatch found.

### Contract 6: GitHub Metadata

All issues must have required labels and milestone:
- **VD:** `type:version-declaration`, milestone, project board (no component label)
- **WO:** `type:work-order`, `component:*`, milestone, project board
- **TASK/FIX:** `type:task`, `component:*`, milestone, project board

---

## References

### Bundled Files

- **`references/planning-runbook-summary.md`** — Stop-gates and invariants checklist
- **`references/template-field-checklist.md`** — Required fields per issue type
- **`references/rci-critique-prompts.md`** — Quality gate prompts for TASK writing
- **`references/github-issue-commands.md`** — Copy/paste-ready commands for Stage 4 + 5

### Scripts

- **`scripts/verify-planning-tree.sh`** — Verify SSOT ↔ GitHub sync

### Canonical Docs (Repo SSOT)

- **`docs/dev/planning/PROJECT-PLANNING-GUIDELINES.md`** — Full planning workflow
- **`docs/dev/GIT_STRATEGY.md`** — Branch model + naming rules
- **`docs/dev/DOCUMENT_PROPAGATION_POLICY.md`** — SSOT + sync rules
- **`.github/ISSUE_TEMPLATE/version-declaration-template.md`** — VD issue template
- **`.github/ISSUE_TEMPLATE/work-order-template.md`** — WO issue template
- **`.github/ISSUE_TEMPLATE/task-template.md`** — TASK issue template

---

## Skill Metadata

**Installation:** `.claude/skills/plan/` (checked into repo)

**Propagation:** Global operational control (SSOT: `develop`, propagates to all active VDs)

**Compatibility:** Claude Code only (user-scope installation)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/markashton480) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
