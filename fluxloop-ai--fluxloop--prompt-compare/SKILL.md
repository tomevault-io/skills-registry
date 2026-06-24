---
name: fluxloop-prompt-compare
description: | Use when this capability is needed.
metadata:
  author: fluxloop-ai
---

# FluxLoop Prompt Compare Skill

**Same Bundle × N Repeats × Version Diff** — freeze inputs via bundle, automate repeated runs, compare outputs.

## Output Format

> 📎 All user-facing output must follow: read skills/_shared/OUTPUT_FORMAT.md

## Context Protocol

1. `fluxloop context show` → confirm project + scenario exist
2. `.fluxloop/test-memory/` check:
   - Exists → load `agent-profile.md`, `results-log.md`
   - Missing → proceed (first run)
3. Dual Write:
   - Server: `fluxloop test --scenario` (×2 runs)
   - Local: save to `prompt-versions.md`, append to `results-log.md`
4. On completion: verify `prompt-versions.md` and `results-log.md` are current

> 📎 Full protocol: read skills/_shared/CONTEXT_PROTOCOL.md
> 📎 Stale detection: read skills/_shared/CONTEXT_COLLECTION.md

## Prerequisite

Run `fluxloop context show` first:
- ✅ Project + scenario exist → proceed to Phase 0
- ❌ No project → Prerequisite Resolution: suggest inline setup execution
- ❌ No scenario → Prerequisite Resolution: suggest inline scenario execution
- Minimum: at least 1 bundle is needed (or will be created in Phase 1)

---

## Phase 0: Context Check

```bash
fluxloop context show
ls .fluxloop/scenarios
```

| State | Action |
|-------|--------|
| No scenario | → "Start with 'create a scenario' (scenario skill)" |
| Scenario exists | → Phase 1 |

**test-memory read**:
1. Read `.fluxloop/test-memory/agent-profile.md`:
   - Extract `git_commit` from metadata → compare with `git rev-parse --short HEAD`
   - Stale → "The profile appears outdated. Would you like to update it?" → Yes → follow `_shared/CONTEXT_COLLECTION.md` inline
2. Read `.fluxloop/test-memory/results-log.md`:
   - If previous test records exist → display as baseline reference

> 📎 Stale detection: read skills/_shared/CONTEXT_COLLECTION.md

---

## Phase 1: Bundle Selection

> 📎 Bundle selection: read skills/_shared/BUNDLE_DECISION.md (simplified flow for comparison tests)

```bash
fluxloop bundles list --scenario-id <scenario_id> --format json
```

> **Tip:** For comparison tests, 1-3 inputs are usually enough. When creating new data, use `--total-count 2` for a small bundle.

Key info to display: **version/name, tag/description, input count, created date**

After bundle selected/created:

```bash
fluxloop sync pull --bundle-version-id <bundle_version_id>
```

> This bundle stays fixed throughout all comparison runs. Record the `bundle_version_id` for reuse.

---

## Phase 2: Comparison Setup

Ask the user:

> 💡 **Repeats**: Measures response consistency (stability) by running the same input multiple times. More repeats produce statistically more reliable comparisons.

```
1. Number of repeats? (default: 5)
2. Multi-turn? (default: single-turn) → if yes, also confirm max turns
3. Current prompt version label? (e.g., "v3", "current version")
```

Set iterations in `configs/simulation.yaml`:

```yaml
iterations: 5  # user-specified count
```

> Read existing simulation.yaml first. Only modify `iterations`, preserve all other fields.

> 📎 Staging environment: read skills/_shared/STAGING.md

---

## Phase 3: Baseline Run (Version A)

### 3-1. Capture git diff (code snapshot)

```bash
git diff HEAD
```

Record the diff output — this captures the current code state before the run.

### 3-2. Run

```bash
# Single-turn (default)
fluxloop test --scenario <name>

# Multi-turn
! fluxloop test --scenario <name> --multi-turn --max-turns <N>
```

> ⚠️ Multi-turn requires `!` prefix.
> 📎 Multi-turn rules: read skills/_shared/MULTITURN.md

After completion:
1. Note experiment directory as `experiment_A`
2. **(Server)**: results stored automatically on server
3. **(Local)**: record Version A in `.fluxloop/test-memory/prompt-versions.md`:
   - Git ref, experiment ID, key characteristics
4. Output — **must include 🔗 link**:
   `✅ Baseline → exp_<timestamp> (label: "v3", N runs) 🔗 https://alpha.app.fluxloop.ai/release/experiments/{experiment_id}/evaluation?project={project_id}`

---

## Phase 4: Prompt Modification

```
Please update the prompt.
Let me know when you're done, and share the new version label (e.g., "v4").
```

Wait for user confirmation. Do NOT modify any code yourself.

---

## Phase 5: Variant Run (Version B)

### 5-1. Capture git diff (what changed)

```bash
git diff HEAD
```

This shows exactly what the user changed between versions. Record for the comparison report.

### 5-2. Run

Same bundle, same inputs — only the prompt changed.

```bash
# Single-turn
fluxloop test --scenario <name>

# Multi-turn (same settings as baseline)
! fluxloop test --scenario <name> --multi-turn --max-turns <N>
```

> No need to `sync pull` again. The bundle is already pulled locally.
> 📎 Multi-turn rules: read skills/_shared/MULTITURN.md

After completion:
1. Note experiment directory as `experiment_B`
2. **(Server)**: results stored automatically on server
3. **(Local)**: add Version B to `.fluxloop/test-memory/prompt-versions.md`:
   - Git ref, experiment ID, changes summary, git diff summary
4. **(Local)**: append comparison entry to `.fluxloop/test-memory/results-log.md`
5. Output — **must include 🔗 link**:
   `✅ Variant → exp_<timestamp> (label: "v4", N runs) 🔗 https://alpha.app.fluxloop.ai/release/experiments/{experiment_id}/evaluation?project={project_id}`

---

## Phase 6: Comparison Analysis

### 6-1. Load Results

Read both experiment trace files:

```
.fluxloop/scenarios/<name>/experiments/<exp_A>/trace_summary.jsonl
.fluxloop/scenarios/<name>/experiments/<exp_B>/trace_summary.jsonl
```

> 📎 Trace structure & analysis formats: read this file's references/analysis-metrics.md

### 6-2. Analyze & Report

Generate a comparison report with these sections:

#### 1) Prompt Changes (git diff summary)

```markdown
## Prompt Changes ({version_A} -> {version_B})
- [Summary of changed files and key edits]
```

#### 2) Per-Input Analysis

Group traces by `input` field, then compare across versions. Use the Per-Input Analysis Format from `references/analysis-metrics.md`.

#### 3) Overall Summary

Use the Overall Summary Table Format from `references/analysis-metrics.md`.

After analysis:
- **(Local)**: update comparison result in `.fluxloop/test-memory/results-log.md`:
  - Winner (A/B/tie), key difference summary
- **(Local)**: update `.fluxloop/test-memory/prompt-versions.md`:
  - Comparison Result section: winner, key difference

---

## Phase 7: Next Actions

```
Choose one:
1. Additional comparison — update prompt again and compare (-> Phase 4)
2. Server evaluation — run detailed analysis with `fluxloop evaluate`
3. Done
```

> 💡 Experiment URLs are already provided in Phase 3 and 5 outputs. Refer to those outputs to review them.

If "Additional comparison": loop back to Phase 4 (same bundle reused).
If "Server evaluation":
```bash
fluxloop evaluate --experiment-id <exp_B_id> --wait
```

---

## Error Handling

| Error | Response |
|-------|----------|
| No scenario exists | "Start with 'create a scenario' (scenario skill)" |
| No bundle available | Guide to bundle creation (Phase 1) |
| Baseline run fails | Check wrapper setup, API key, network. Resolve before continuing. |
| Variant run fails | Same check. Do NOT compare partial results. |
| trace_summary.jsonl missing | Check experiment directory. Re-run if needed. |
| Different input counts between A/B | This should not happen (same bundle). Verify bundle_version_id. |
| Profile stale (git_commit mismatch) | Offer inline update via _shared/CONTEXT_COLLECTION.md |

## Next Steps

Comparison done. Available next actions:
- Loop back to Phase 4 for another comparison (same bundle reused)
- Deep analysis with server evaluation (evaluate skill)
- Run a full test with winning prompt (test skill)
- Refine scenario based on learnings (scenario skill)

## Quick Reference

| Step | Command |
|------|---------|
| Check | `fluxloop context show` |
| Bundle | `fluxloop bundles list --scenario-id <id>` |
| Pull | `fluxloop sync pull --bundle-version-id <id>` |
| Run | `fluxloop test --scenario <name>` |
| Evaluate | `fluxloop evaluate --experiment-id <id> --wait` |

> 📎 Full CLI reference: read skills/_shared/QUICK_REFERENCE.md

## Key Rules

1. **Inputs come from Web** — generate via `inputs synthesize` or select from existing, never write base_inputs manually
2. **Small bundles for comparison** — recommend `--total-count 2` when generating new inputs for comparison
3. **Bundle = frozen inputs** — pull once, reuse across all comparison runs
4. **Never modify the user's prompt/agent code** — only the user does that
5. **Capture git diff before each run** — links code changes to result changes
6. **Only change `iterations` in simulation.yaml** — preserve all other config fields
7. **Always read trace_summary.jsonl** — it has per-run details needed for comparison
8. **Label experiments clearly** — use user-provided version labels throughout
9. **Multi-turn uses `!` prefix** — same as other skills
10. **Check `agent-profile.md` for staleness before starting** — update if git_commit mismatches
11. **Dual Write**: record versions to `prompt-versions.md` and results to `results-log.md` alongside server actions
12. **Use templates from `test-memory-template/`** for output format

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/fluxloop-ai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
