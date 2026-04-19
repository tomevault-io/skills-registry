---
name: verify-requirements
description: > Use when this capability is needed.
metadata:
  author: shirou
---

# Post-Implementation Requirements Verification

Verify that a completed implementation satisfies its linked TDL requirements.

## Workflow

```
1. Identify task → 2. Collect requirements → 3. Analyze code → 4. Generate report → 5. Update docs
```

### Step 1: Identify Task

Determine the target task from user input. Accept any of:
- Full task ID: `T-r7k2p-mission-execution-sequencer`
- Short ID: `T-r7k2p`
- Task directory path: `docs/tasks/T-r7k2p-mission-execution-sequencer/`

Find the task directory by searching `docs/tasks/T-<id>-*/`.

Read these files:
- `README.md` — extract **Related Requirements** links (FR-\*, NFR-\*)
- `plan.md` — extract **Known Gaps**, **Definition of Done**, phase completion status

### Step 2: Collect Requirements

For each requirement linked in the task README:

1. Read the requirement file (`docs/requirements/FR-<id>-<name>.md` or `NFR-<id>-<name>.md`)
2. Extract **Acceptance Criteria** (the `- [ ]` / `- [x]` checklist items)
3. Note the requirement type: Functional (FR) vs Non-Functional (NFR)

Classify each criterion by verification method. See [references/verification-guide.md](references/verification-guide.md) for details.

### Step 3: Analyze Implementation

For each acceptance criterion, determine its status using evidence:

- **Code inspection**: Search for traits, structs, functions; verify signatures and error handling
- **Test verification**: Search for unit tests covering the criterion; run `cargo test --lib --quiet` if needed
- **Build verification**: Check `./scripts/build-rp2350.sh` for embedded requirements
- **Static analysis**: `cargo clippy`, check for `unsafe` code, verify `no_std` compatibility
- **Documentation check**: Verify docs exist, run `bun scripts/trace-status.ts --check`

### Step 4: Generate Report

Produce a verification report. See [references/verification-guide.md](references/verification-guide.md) for the full template and status definitions.

Summary format:

```markdown
# Verification Report: T-<id>-<name>

## Summary
| Metric | Count |
|--------|-------|
| Total criteria | N |
| PASS | N |
| FAIL | N |
| PARTIAL | N |
| N/V | N |

## Results by Requirement
### FR-<id>-<name>
| # | Criterion | Status | Evidence |
|---|-----------|--------|----------|
| 1 | <text> | PASS | <file:line, test name, etc.> |
```

### Step 5: Update Documents (optional, with user approval)

Ask the user before modifying any files. If approved:
- Mark `- [x]` for PASS criteria in requirement files
- Leave `- [ ]` for FAIL/PARTIAL/N/V criteria
- Add Known Gaps to `plan.md` for FAIL/PARTIAL items not already documented

## Rules

- **Evidence required**: Every status must cite `file:line`, test names, or command outputs
- **Conservative judgment**: When uncertain, use PARTIAL or N/V rather than PASS
- **Performance criteria**: NFRs about timing/memory/latency are N/V unless benchmarks exist
- **Known gaps first**: Check plan.md Known Gaps before analyzing — pre-identified issues save time
- **Scope awareness**: Only verify criteria relevant to the task's stated scope

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/shirou) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
