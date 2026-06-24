---
name: code-review
description: Bug/security/quality review of source + automated fix application, consolidated. Use after a phase or PR diff to catch regressions before ship. Use when this capability is needed.
metadata:
  author: produtoramaxvision
---

## Section: Code Review

<purpose>
Review source files changed during a phase for bugs, security issues, and code quality problems. Computes file scope (--files override > SUMMARY.md > git diff fallback), checks config gate, spawns maxvision-code-reviewer agent, commits REVIEW.md, and presents results to user.
</purpose>

<required_reading>
Read all files referenced by the invoking prompt's execution_context before starting.
</required_reading>

<available_agent_types>
- maxvision-code-reviewer: Reviews source files for bugs and quality issues
</available_agent_types>

<process>

<step name="initialize">
Parse arguments and load project state:

```bash
set -euo pipefail
PHASE_ARG="${1}"
INIT=$(maxvision-sdk query init.phase-op "${PHASE_ARG}")
if [[ "$INIT" == @file:* ]]; then INIT=$(cat "${INIT#@file:}"); fi
```

Parse from init JSON: `phase_found`, `phase_dir`, `phase_number`, `phase_name`, `padded_phase`, `commit_docs`.

**Input sanitization (defense-in-depth):**
```bash
# Validate PADDED_PHASE contains only digits and optional dot (e.g., "02", "03.1")
set -euo pipefail
if ! [[ "$PADDED_PHASE" =~ ^[0-9]+(\.[0-9]+)?$ ]]; then
  echo "Error: Invalid phase number format: '${PADDED_PHASE}'. Expected digits (e.g., 02, 03.1)."
  # Exit workflow
fi
```

**Phase validation (before config gate):**
If `phase_found` is false, report error and exit:
```
Error: Phase ${PHASE_ARG} not found. Run /maxvision-progress to see available phases.
```

This runs BEFORE config gate check so user errors are surfaced immediately regardless of config state.

Parse optional flags from $ARGUMENTS:

**--depth flag:**
```bash
set -euo pipefail
DEPTH_OVERRIDE=""
for arg in "$@"; do
  if [[ "$arg" == --depth=* ]]; then
    DEPTH_OVERRIDE="${arg#--depth=}"
  fi
done
```

**--files flag:**
```bash
set -euo pipefail
FILES_OVERRIDE=""
for arg in "$@"; do
  if [[ "$arg" == --files=* ]]; then
    FILES_OVERRIDE="${arg#--files=}"
  fi
done
```

If FILES_OVERRIDE is set, split by comma into array:
```bash
set -euo pipefail
if [ -n "$FILES_OVERRIDE" ]; then
  IFS=',' read -ra FILES_ARRAY <<< "$FILES_OVERRIDE"
fi
```
</step>

<step name="check_config_gate">
Check if code review is enabled via config:

```bash
set -euo pipefail
CODE_REVIEW_ENABLED=$(maxvision-sdk query config-get workflow.code_review 2>/dev/null || echo "true")
```

If CODE_REVIEW_ENABLED is "false":
```
Code review skipped (workflow.code_review=false in config)
```
Exit workflow.

Default is true — only skip on explicit false. This check runs AFTER phase validation so invalid phase errors are shown first.
</step>

<step name="resolve_depth">
Determine review depth with priority order:

1. DEPTH_OVERRIDE from --depth flag (highest priority)
2. Config value: `maxvision-sdk query config-get workflow.code_review_depth 2>/dev/null`
3. Default: "standard"

```bash
set -euo pipefail
if [ -n "$DEPTH_OVERRIDE" ]; then
  REVIEW_DEPTH="$DEPTH_OVERRIDE"
else
  CONFIG_DEPTH=$(maxvision-sdk query config-get workflow.code_review_depth 2>/dev/null || echo "")
  REVIEW_DEPTH="${CONFIG_DEPTH:-standard}"
fi
```

**Validate depth value:**
```bash
set -euo pipefail
case "$REVIEW_DEPTH" in
  quick|standard|deep)
    # Valid
    ;;
  *)
    echo "Warning: Invalid depth '${REVIEW_DEPTH}'. Valid values: quick, standard, deep. Using 'standard'."
    REVIEW_DEPTH="standard"
    ;;
esac
```
</step>

<step name="compute_file_scope">
Three-tier scoping with explicit precedence:

**Tier 1 — --files override (highest precedence per D-08):**

If FILES_OVERRIDE is set (from --files flag):
```bash
set -euo pipefail
if [ -n "$FILES_OVERRIDE" ]; then
  REVIEW_FILES=()
  REPO_ROOT=$(git rev-parse --show-toplevel 2>/dev/null)
  
  for file_path in "${FILES_ARRAY[@]}"; do
    # Security: validate path is within repository (prevent path traversal)
    ABS_PATH=$(realpath -m "${file_path}" 2>/dev/null || echo "${file_path}")
    if [[ "$ABS_PATH" != "$REPO_ROOT"* ]]; then
      echo "Error: File path outside repository, skipping: ${file_path}"
      continue
    fi
    
    # Validate path exists (relative to repo root)
    if [ -f "${REPO_ROOT}/${file_path}" ] || [ -f "${file_path}" ]; then
      REVIEW_FILES+=("$file_path")
    else
      echo "Warning: File not found, skipping: ${file_path}"
    fi
  done
  
  echo "File scope: ${#REVIEW_FILES[@]} files from --files override"
fi
```

Skip SUMMARY/git scoping entirely when --files is provided.

**Tier 2 — SUMMARY.md extraction (primary per D-01):**

If --files NOT provided:
```bash
set -euo pipefail
if [ -z "$FILES_OVERRIDE" ]; then
  SUMMARIES=$(ls "${PHASE_DIR}"/*-SUMMARY.md 2>/dev/null)
  REVIEW_FILES=()
  
  if [ -n "$SUMMARIES" ]; then
    for summary in $SUMMARIES; do
      # Extract key_files.created and key_files.modified using node for reliable YAML parsing
      # This avoids fragile awk parsing that breaks on indentation differences
      EXTRACTED=$(node -e "
        const fs = require('fs');
        const content = fs.readFileSync('$summary', 'utf-8');
        const match = content.match(/^---\n([\s\S]*?)\n---/);
        if (!match) { process.exit(0); }
        const yaml = match[1];
        const files = [];
        let inSection = null;
        for (const line of yaml.split('\n')) {
          if (/^\s+created:/.test(line)) { inSection = 'created'; continue; }
          if (/^\s+modified:/.test(line)) { inSection = 'modified'; continue; }
          if (/^\s*[\w-]+:/.test(line) && !/^\s*-/.test(line)) { inSection = null; continue; }
          if (inSection && /^\s+-\s+(.+)/.test(line)) {
            let raw = line.match(/^\s+-\s+(.+)/)[1].trim();
            raw = raw.replace(/^['"]|['"]$/g, '');
            raw = raw.replace(/\s+\([^)]*\)\s*$/, '');
            raw = raw.split(/\s+—\s/)[0].trim();
            if (/\//.test(raw) && /\.[A-Za-z0-9]+$/.test(raw)) {
              files.push(raw);
            }
          }
        }
        if (files.length) console.log(files.join('\n'));
      " 2>/dev/null)
      
      # Add extracted files to REVIEW_FILES array
      if [ -n "$EXTRACTED" ]; then
        while IFS= read -r file; do
          if [ -n "$file" ]; then
            REVIEW_FILES+=("$file")
          fi
        done <<< "$EXTRACTED"
      fi
    done
    
    if [ ${#REVIEW_FILES[@]} -eq 0 ]; then
      echo "Warning: SUMMARY artifacts found but contained no file paths. Falling back to git diff."
    fi
  fi
fi
```

**Tier 3 — Git diff fallback (per D-02):**

If no SUMMARY.md files found OR no files extracted from them:
```bash
set -euo pipefail
if [ ${#REVIEW_FILES[@]} -eq 0 ]; then
  # Compute diff base from phase commits — fail closed if no reliable base found
  PHASE_COMMITS=$(git log --oneline --all --grep="${PADDED_PHASE}" --format="%H" 2>/dev/null)
  
  if [ -n "$PHASE_COMMITS" ]; then
    DIFF_BASE=$(echo "$PHASE_COMMITS" | tail -1)^
    
    # Verify the parent commit exists (first commit in repo has no parent)
    if ! git rev-parse "${DIFF_BASE}" >/dev/null 2>&1; then
      DIFF_BASE=$(echo "$PHASE_COMMITS" | tail -1)
    fi
    
    # Run git diff with specific exclusions (per D-03)
    DIFF_FILES=$(git diff --name-only "${DIFF_BASE}..HEAD" -- . \
      ':!.maxvision/' ':!ROADMAP.md' ':!STATE.md' \
      ':!*-SUMMARY.md' ':!*-VERIFICATION.md' ':!*-PLAN.md' \
      ':!package-lock.json' ':!yarn.lock' ':!Gemfile.lock' ':!poetry.lock' 2>/dev/null)
    
    while IFS= read -r file; do
      [ -n "$file" ] && REVIEW_FILES+=("$file")
    done <<< "$DIFF_FILES"
    
    echo "File scope: ${#REVIEW_FILES[@]} files from git diff (base: ${DIFF_BASE})"
  else
    # Fail closed — no reliable diff base found. Do not use arbitrary HEAD~N.
    echo "Warning: No phase commits found for '${PADDED_PHASE}'. Cannot determine reliable diff scope."
    echo "Use --files flag to specify files explicitly: /maxvision-code-review ${PHASE_ARG} --files=file1,file2,..."
  fi
fi
```

**Post-processing (all tiers):**

1. **Apply exclusions (per D-03):** Remove paths matching planning artifacts
```bash
set -euo pipefail
FILTERED_FILES=()
for file in "${REVIEW_FILES[@]}"; do
  # Skip planning directory and specific artifacts
  if [[ "$file" == .maxvision/* ]] || \
     [[ "$file" == ROADMAP.md ]] || \
     [[ "$file" == STATE.md ]] || \
     [[ "$file" == *-SUMMARY.md ]] || \
     [[ "$file" == *-VERIFICATION.md ]] || \
     [[ "$file" == *-PLAN.md ]]; then
    continue
  fi
  FILTERED_FILES+=("$file")
done
REVIEW_FILES=("${FILTERED_FILES[@]}")
```

2. **Filter deleted files:** Remove paths that don't exist on disk
```bash
set -euo pipefail
EXISTING_FILES=()
DELETED_COUNT=0
for file in "${REVIEW_FILES[@]}"; do
  if [ -f "$file" ]; then
    EXISTING_FILES+=("$file")
  else
    DELETED_COUNT=$((DELETED_COUNT + 1))
  fi
done
REVIEW_FILES=("${EXISTING_FILES[@]}")

if [ $DELETED_COUNT -gt 0 ]; then
  echo "Filtered $DELETED_COUNT deleted files from review scope"
fi
```

3. **Deduplicate:** Remove duplicate paths (portable — bash 3.2+ compatible, handles spaces in paths)
```bash
set -euo pipefail
DEDUPED=()
while IFS= read -r line; do
  [ -n "$line" ] && DEDUPED+=("$line")
done < <(printf '%s\n' "${REVIEW_FILES[@]}" | sort -u)
REVIEW_FILES=("${DEDUPED[@]}")
```

4. **Sort:** Alphabetical sort for reproducible agent input (already sorted by sort -u above)

**Log final scope and warn if large:**
```bash
set -euo pipefail
if [ -n "$FILES_OVERRIDE" ]; then
  TIER="--files override"
elif [ -n "$SUMMARIES" ] && [ ${#REVIEW_FILES[@]} -gt 0 ]; then
  TIER="SUMMARY.md"
else
  TIER="git diff"
fi
echo "File scope: ${#REVIEW_FILES[@]} files from ${TIER}"

# Warn if file count is very large — may exceed agent context or produce superficial review
if [ ${#REVIEW_FILES[@]} -gt 50 ]; then
  echo "Warning: ${#REVIEW_FILES[@]} files is a large review scope."
  echo "Consider using --files to narrow scope, or --depth=quick for a faster pass."
  if [ "$REVIEW_DEPTH" = "deep" ]; then
    echo "Switching from deep to standard depth for large file count."
    REVIEW_DEPTH="standard"
  fi
fi
```
</step>

<step name="check_empty_scope">
If REVIEW_FILES is empty:
```
No source files changed in phase ${PHASE_ARG}. Skipping review.
```
Exit workflow. Do NOT spawn agent or create REVIEW.md.
</step>

<step name="spawn_reviewer">
Compute the review output path:
```bash
set -euo pipefail
REVIEW_PATH="${PHASE_DIR}/${PADDED_PHASE}-REVIEW.md"
```

Compute DIFF_BASE for agent context (in case agent needs it):
```bash
set -euo pipefail
PHASE_COMMITS=$(git log --oneline --all --grep="${PADDED_PHASE}" --format="%H" 2>/dev/null)
if [ -n "$PHASE_COMMITS" ]; then
  DIFF_BASE=$(echo "$PHASE_COMMITS" | tail -1)^
else
  DIFF_BASE=""
fi
```

Build files_to_read block for agent:
```bash
set -euo pipefail
FILES_TO_READ=""
for file in "${REVIEW_FILES[@]}"; do
  FILES_TO_READ+="- ${file}\n"
done
```

Build config block for agent:
```bash
set -euo pipefail
CONFIG_FILES=""
for file in "${REVIEW_FILES[@]}"; do
  CONFIG_FILES+="  - ${file}\n"
done
```

Spawn the maxvision-code-reviewer agent:

```
Agent(subagent_type="maxvision-code-reviewer", prompt="
<files_to_read>
${FILES_TO_READ}
</files_to_read>

<config>
depth: ${REVIEW_DEPTH}
phase_dir: ${PHASE_DIR}
review_path: ${REVIEW_PATH}
${DIFF_BASE:+diff_base: ${DIFF_BASE}}
files:
${CONFIG_FILES}
</config>

Review the listed source files at ${REVIEW_DEPTH} depth. Write findings to ${REVIEW_PATH}.
Do NOT commit the output — the orchestrator handles that.
")
```

> **ORCHESTRATOR RULE — CODEX RUNTIME**: After calling Agent() above, stop working on this task immediately. Do not read more files, edit code, or run tests related to this task while the subagent is active. Wait for the subagent to return its result. This prevents duplicate work, conflicting edits, and wasted context. Only resume when the subagent result is available.

**Agent failure handling:**

If the Agent() call fails (agent error, timeout, or exception):
```
Error: Code review agent failed: ${error_message}

No REVIEW.md created. You can retry with /maxvision-code-review ${PHASE_ARG} or check agent logs.
```

Do NOT proceed to commit_review step. Do NOT create a partial or empty REVIEW.md. Exit workflow.
</step>

<step name="commit_review">
After agent completes successfully, verify REVIEW.md was created and has valid structure:

```bash
set -euo pipefail
if [ -f "${REVIEW_PATH}" ]; then
  # Validate REVIEW.md has valid YAML frontmatter with status field
  HAS_STATUS=$(REVIEW_PATH="${REVIEW_PATH}" node -e "
    const fs = require('fs');
    const content = fs.readFileSync(process.env.REVIEW_PATH, 'utf-8');
    const match = content.match(/^---\n([\s\S]*?)\n---/);
    if (match && /status:/.test(match[1])) { console.log('valid'); } else { console.log('invalid'); }
  " 2>/dev/null)
  
  if [ "$HAS_STATUS" = "valid" ]; then
    echo "REVIEW.md created at ${REVIEW_PATH}"
    
    if [ "$COMMIT_DOCS" = "true" ]; then
      maxvision-sdk query commit \
        "docs(${PADDED_PHASE}): add code review report" \
        --files "${REVIEW_PATH}"
    fi
  else
    echo "Warning: REVIEW.md exists but has invalid or missing frontmatter (no status field)."
    echo "Agent may have produced malformed output. Not committing. Review manually: ${REVIEW_PATH}"
  fi
else
  echo "Warning: Agent completed but REVIEW.md not found at ${REVIEW_PATH}. This may indicate an agent issue."
  echo "No REVIEW.md to commit. Please retry with /maxvision-code-review ${PHASE_ARG}"
fi
```
</step>

<step name="present_results">
Read the REVIEW.md YAML frontmatter to extract finding counts.

Extract frontmatter between `---` delimiters first to avoid matching values in the review body:

```bash
# Extract only the YAML frontmatter block (between first two --- lines)
set -euo pipefail
FRONTMATTER=$(REVIEW_PATH="${REVIEW_PATH}" node -e "
  const fs = require('fs');
  const content = fs.readFileSync(process.env.REVIEW_PATH, 'utf-8');
  const match = content.match(/^---\n([\s\S]*?)\n---/);
  if (match) process.stdout.write(match[1]);
" 2>/dev/null)

# Parse fields from frontmatter only (not full file)
STATUS=$(echo "$FRONTMATTER" | grep "^status:" | cut -d: -f2 | xargs)
FILES_REVIEWED=$(echo "$FRONTMATTER" | grep "^files_reviewed:" | cut -d: -f2 | xargs)
CRITICAL=$(echo "$FRONTMATTER" | grep -E "^[[:space:]]*(critical|blocker):" | head -1 | cut -d: -f2 | xargs)
WARNING=$(echo "$FRONTMATTER" | grep "warning:" | head -1 | cut -d: -f2 | xargs)
INFO=$(echo "$FRONTMATTER" | grep "info:" | head -1 | cut -d: -f2 | xargs)
TOTAL=$(echo "$FRONTMATTER" | grep "total:" | head -1 | cut -d: -f2 | xargs)
```

Display inline summary to user:

```
═══════════════════════════════════════════════════════════════

  Code Review Complete: Phase ${PHASE_NUMBER} (${PHASE_NAME})

───────────────────────────────────────────────────────────────

  Depth:           ${REVIEW_DEPTH}
  Files Reviewed:  ${FILES_REVIEWED}
  
  Findings:
    Critical:  ${CRITICAL}
    Warning:   ${WARNING}
    Info:      ${INFO}
    ──────────
    Total:     ${TOTAL}

───────────────────────────────────────────────────────────────
```

If status is "clean":
```
✓ No issues found. All ${FILES_REVIEWED} files pass review at ${REVIEW_DEPTH} depth.

Full report: ${REVIEW_PATH}
```

If total findings > 0:
```
⚠ Issues found. Review the report for details.

Full report: ${REVIEW_PATH}

Next steps:
  /maxvision-code-review ${PHASE_NUMBER} --fix  — Auto-fix issues
  cat ${REVIEW_PATH}                     — View full report
```

If critical > 0 or warning > 0, list top 3 issues inline:
```bash
set -euo pipefail
echo "Top issues:"
grep -A 3 "^### CR-\|^### BL-\|^### WR-" "${REVIEW_PATH}" | head -n 12
```

**Note on tests:** Automated tests for this command and workflow are planned for Phase 4 (Pipeline Integration & Testing, requirement INFR-03). Phase 2 focuses on correct implementation; Phase 4 adds regression coverage across platforms.

═══════════════════════════════════════════════════════════════
</step>

</process>

<platform_notes>
**Windows:** This workflow uses bash features (arrays, process substitution). On Windows, it requires
Git Bash or WSL. Native PowerShell is not supported. The CI matrix (Ubuntu/macOS/Windows)
runs under Git Bash on Windows runners, which provides bash compatibility.

**macOS:** macOS ships with bash 3.2 (GPL licensing). This workflow does NOT use `mapfile` (bash 4+
only) — all array construction uses portable `while IFS= read -r` loops compatible with bash 3.2.
The `--files` path validation uses `realpath -m` which requires GNU coreutils (install via
`brew install coreutils`). Without coreutils, the path guard falls back to fail-closed behavior
(rejects paths it cannot verify), so security is maintained but valid relative paths may be rejected.
If `--files` validation fails unexpectedly on macOS, install coreutils or use absolute paths.
</platform_notes>

<success_criteria>
- [ ] Phase validated before config gate check
- [ ] Config gate checked (workflow.code_review)
- [ ] Depth resolved with validation (quick|standard|deep)
- [ ] File scope computed with 3 tiers: --files > SUMMARY.md > git diff
- [ ] Malformed/missing SUMMARY.md handled gracefully with fallback
- [ ] Deleted files filtered from scope
- [ ] Files deduplicated and sorted
- [ ] Empty scope results in skip (no agent spawn)
- [ ] Agent spawned with explicit file list, depth, review_path, diff_base
- [ ] Agent failure handled without partial commits
- [ ] REVIEW.md committed if created
- [ ] Results presented inline with next step suggestion
</success_criteria>


---

## Section: Code Review Fix

<purpose>
Auto-fix issues from REVIEW.md. Validates phase, checks config gate, verifies REVIEW.md exists and has fixable issues, spawns maxvision-code-fixer agent, handles --auto iteration loop (capped at 3), commits REVIEW-FIX.md once at the end, and presents results.
</purpose>

<required_reading>
Read all files referenced by the invoking prompt's execution_context before starting.
</required_reading>

<available_agent_types>
- maxvision-code-fixer: Applies fixes to code review findings
- maxvision-code-reviewer: Reviews source files for bugs and issues
</available_agent_types>

<process>

<step name="initialize">
Parse arguments and load project state:

```bash
set -euo pipefail
PHASE_ARG="${1}"
INIT=$(maxvision-sdk query init.phase-op "${PHASE_ARG}")
if [[ "$INIT" == @file:* ]]; then INIT=$(cat "${INIT#@file:}"); fi
```

Parse from init JSON: `phase_found`, `phase_dir`, `phase_number`, `phase_name`, `padded_phase`, `commit_docs`.

**Input sanitization (defense-in-depth):**
```bash
# Validate PADDED_PHASE contains only digits and optional dot (e.g., "02", "03.1")
set -euo pipefail
if ! [[ "$PADDED_PHASE" =~ ^[0-9]+(\.[0-9]+)?$ ]]; then
  echo "Error: Invalid phase number format: '${PADDED_PHASE}'. Expected digits (e.g., 02, 03.1)."
  # Exit workflow
fi
```

**Phase validation (before config gate):**
If `phase_found` is false, report error and exit:
```
Error: Phase ${PHASE_ARG} not found. Run /maxvision-progress to see available phases.
```

This runs BEFORE config gate check so user errors are surfaced immediately regardless of config state.

Parse optional flags from $ARGUMENTS:

```bash
set -euo pipefail
FIX_ALL=false
AUTO_MODE=false
for arg in "$@"; do
  if [[ "$arg" == "--all" ]]; then FIX_ALL=true; fi
  if [[ "$arg" == "--auto" ]]; then AUTO_MODE=true; fi
done
```

Compute scope variable:

```bash
set -euo pipefail
if [ "$FIX_ALL" = "true" ]; then
  FIX_SCOPE="all"
else
  FIX_SCOPE="critical_warning"
fi
```

Compute review and fix report paths:

```bash
set -euo pipefail
REVIEW_PATH="${PHASE_DIR}/${PADDED_PHASE}-REVIEW.md"
FIX_REPORT_PATH="${PHASE_DIR}/${PADDED_PHASE}-REVIEW-FIX.md"
```
</step>

<step name="check_config_gate">
Check if code review is enabled via config:

```bash
set -euo pipefail
CODE_REVIEW_ENABLED=$(maxvision-sdk query config-get workflow.code_review 2>/dev/null || echo "true")
```

If CODE_REVIEW_ENABLED is "false":
```
Code review fix skipped (workflow.code_review=false in config)
```
Exit workflow.

Default is true — only skip on explicit false. This check runs AFTER phase validation so invalid phase errors are shown first.

Note: This reuses the `workflow.code_review` config key rather than introducing a separate `workflow.code_review_fix` key. Rationale: fixes are meaningless without review, so a single toggle makes sense. If independent control is needed later, a separate key can be added in v2.
</step>

<step name="check_review_exists">
Verify that REVIEW.md exists:

```bash
set -euo pipefail
if [ ! -f "${REVIEW_PATH}" ]; then
  echo "Error: No REVIEW.md found for Phase ${PHASE_ARG}. Run /maxvision-code-review ${PHASE_ARG} first."
  exit 1
fi
```

Do NOT auto-run code-review. Require explicit user action to ensure review intent is clear.
</step>

<step name="check_review_status">
Parse REVIEW.md frontmatter to check status and extract context for --auto loop:

```bash
# Parse status field
set -euo pipefail
REVIEW_STATUS=$(REVIEW_PATH="${REVIEW_PATH}" node -e "
  const fs = require('fs');
  const content = fs.readFileSync(process.env.REVIEW_PATH, 'utf-8');
  const match = content.match(/^---\n([\s\S]*?)\n---/);
  if (match && /status:\s*(\S+)/.test(match[1])) {
    console.log(match[1].match(/status:\s*(\S+)/)[1]);
  } else {
    console.log('unknown');
  }
" 2>/dev/null)
```

If status is "clean" or "skipped":
```
No issues to fix in Phase ${PHASE_ARG} REVIEW.md (status: ${REVIEW_STATUS}).
```
Exit workflow.

If status is "unknown":
```
Warning: Could not parse REVIEW.md status. Proceeding with fix attempt.
```

Extract review depth for --auto re-review:

```bash
set -euo pipefail
REVIEW_DEPTH=$(REVIEW_PATH="${REVIEW_PATH}" node -e "
  const fs = require('fs');
  const content = fs.readFileSync(process.env.REVIEW_PATH, 'utf-8');
  const match = content.match(/^---\n([\s\S]*?)\n---/);
  if (match && /depth:\s*(\S+)/.test(match[1])) {
    console.log(match[1].match(/depth:\s*(\S+)/)[1]);
  } else {
    console.log('standard');
  }
" 2>/dev/null)
```

Extract original review file list for --auto re-review scope persistence:

```bash
# Extract review file list — portable bash 3.2+ (no mapfile, handles spaces in paths)
set -euo pipefail
REVIEW_FILES_ARRAY=()
while IFS= read -r line; do
  [ -n "$line" ] && REVIEW_FILES_ARRAY+=("$line")
done < <(REVIEW_PATH="${REVIEW_PATH}" node -e "
  const fs = require('fs');
  const content = fs.readFileSync(process.env.REVIEW_PATH, 'utf-8');
  const match = content.match(/^---\n([\s\S]*?)\n---/);
  if (match) {
    const fm = match[1];
    // Try YAML array format: files_reviewed_list: [file1, file2]
    const bracketMatch = fm.match(/files_reviewed_list:\s*\[([^\]]+)\]/);
    if (bracketMatch) {
      bracketMatch[1].split(',').map(f => f.trim()).filter(Boolean).forEach(f => console.log(f));
    } else {
      // Try YAML list format: files_reviewed_list:\n  - file1\n  - file2
      let inList = false;
      for (const line of fm.split('\n')) {
        if (/files_reviewed_list:/.test(line)) { inList = true; continue; }
        if (inList && /^\s+-\s+(.+)/.test(line)) { console.log(line.match(/^\s+-\s+(.+)/)[1].trim()); }
        else if (inList && /^\S/.test(line)) { break; }
      }
    }
  }
" 2>/dev/null)
```

If REVIEW.md contains a `files_reviewed_list` frontmatter field, use that as the re-review scope. If not present, fall back to re-reviewing the full phase (same behavior as initial code-review).
</step>

<step name="spawn_fixer">
Spawn the maxvision-code-fixer agent with config:

```bash
# Build config for agent
set -euo pipefail
echo "Applying fixes from ${REVIEW_PATH}..."
echo "Fix scope: ${FIX_SCOPE}"
```

Use Agent() to spawn agent:

```text
Agent(subagent_type="maxvision-code-fixer", prompt="
<files_to_read>
${REVIEW_PATH}
</files_to_read>

<config>
phase_dir: ${PHASE_DIR}
padded_phase: ${PADDED_PHASE}
review_path: ${REVIEW_PATH}
fix_scope: ${FIX_SCOPE}
fix_report_path: ${FIX_REPORT_PATH}
iteration: 1
</config>

Read REVIEW.md findings, apply fixes, commit each atomically, write REVIEW-FIX.md. Do NOT commit REVIEW-FIX.md (orchestrator handles that).
")
```

> **ORCHESTRATOR RULE — CODEX RUNTIME**: After calling Agent() above, stop working on this task immediately. Do not read more files, edit code, or run tests related to this task while the subagent is active. Wait for the subagent to return its result. This prevents duplicate work, conflicting edits, and wasted context. Only resume when the subagent result is available.

**Agent failure handling:**

If Agent() fails:
```
Error: Code fix agent failed: ${error_message}
```

Check if FIX_REPORT_PATH exists:
- If yes: "Partial success — some fixes may have been committed."
- If no: "No fixes applied."

Either way:
```
Some fix commits may already exist in git history — check git log for fix(${PADDED_PHASE}) commits.
You can retry with /maxvision-code-review ${PHASE_ARG} --fix.
```

Exit workflow (skip auto loop).
</step>

<step name="auto_iteration_loop">
Only runs if AUTO_MODE is true. If AUTO_MODE is false, skip this step entirely.

```bash
set -euo pipefail
if [ "$AUTO_MODE" = "true" ]; then
  # Iteration semantics: the initial fix pass (step 5) is iteration 1.
  # This loop runs iterations 2..MAX_ITERATIONS (re-review + re-fix cycles).
  # Total fix passes = MAX_ITERATIONS. Loop uses -lt (not -le) intentionally.
  ITERATION=1
  MAX_ITERATIONS=3
  
  while [ $ITERATION -lt $MAX_ITERATIONS ]; do
    ITERATION=$((ITERATION + 1))
    
    echo ""
    echo "═══════════════════════════════════════════════════════"
    echo "  --auto: Starting iteration ${ITERATION}/${MAX_ITERATIONS}"
    echo "═══════════════════════════════════════════════════════"
    echo ""
    
    # Re-review using same depth and file scope as original review
    echo "Re-reviewing phase ${PHASE_ARG} at ${REVIEW_DEPTH} depth..."
    
    # Backup previous REVIEW.md and REVIEW-FIX.md before overwriting
    if [ -f "${REVIEW_PATH}" ]; then
      cp "${REVIEW_PATH}" "${REVIEW_PATH%.md}.iter${ITERATION}.md" 2>/dev/null || true
    fi
    if [ -f "${FIX_REPORT_PATH}" ]; then
      cp "${FIX_REPORT_PATH}" "${FIX_REPORT_PATH%.md}.iter${ITERATION}.md" 2>/dev/null || true
    fi
    
    # If original review had explicit file list, pass it safely to re-review agent
    FILES_CONFIG=""
    if [ ${#REVIEW_FILES_ARRAY[@]} -gt 0 ]; then
      FILES_CONFIG="files:"
      for f in "${REVIEW_FILES_ARRAY[@]}"; do
        FILES_CONFIG="${FILES_CONFIG}
  - ${f}"
      done
    fi
    
    # Spawn maxvision-code-reviewer agent to re-review
    # (This overwrites REVIEW_PATH with latest review state)
    Agent(subagent_type="maxvision-code-reviewer", prompt="
<config>
depth: ${REVIEW_DEPTH}
phase_dir: ${PHASE_DIR}
review_path: ${REVIEW_PATH}
${FILES_CONFIG}
</config>

Re-review the phase at ${REVIEW_DEPTH} depth. Write findings to ${REVIEW_PATH}.
Do NOT commit the output — the orchestrator handles that.
")
    # ORCHESTRATOR RULE — CODEX RUNTIME: After calling Agent() above, stop working on this task immediately. Do not read more files, edit code, or run tests related to this task while the subagent is active. Wait for the subagent to return its result before proceeding.
    
    # Check new REVIEW.md status
    NEW_STATUS=$(REVIEW_PATH="${REVIEW_PATH}" node -e "
      const fs = require('fs');
      const content = fs.readFileSync(process.env.REVIEW_PATH, 'utf-8');
      const match = content.match(/^---\n([\s\S]*?)\n---/);
      if (match && /status:\s*(\S+)/.test(match[1])) {
        console.log(match[1].match(/status:\s*(\S+)/)[1]);
      } else {
        console.log('unknown');
      }
    " 2>/dev/null)
    
    if [ "$NEW_STATUS" = "clean" ]; then
      echo ""
      echo "✓ All issues resolved after iteration ${ITERATION}."
      break
    fi
    
    # Still has issues — spawn fixer again
    echo "Issues remain. Applying fixes for iteration ${ITERATION}..."
    
    Agent(subagent_type="maxvision-code-fixer", prompt="
<files_to_read>
${REVIEW_PATH}
</files_to_read>

<config>
phase_dir: ${PHASE_DIR}
padded_phase: ${PADDED_PHASE}
review_path: ${REVIEW_PATH}
fix_scope: ${FIX_SCOPE}
fix_report_path: ${FIX_REPORT_PATH}
iteration: ${ITERATION}
</config>

Read REVIEW.md findings, apply fixes, commit each atomically, write REVIEW-FIX.md (overwrite previous). Do NOT commit REVIEW-FIX.md.
")
    # ORCHESTRATOR RULE — CODEX RUNTIME: After calling Agent() above, stop working on this task immediately. Do not read more files, edit code, or run tests related to this task while the subagent is active. Wait for the subagent to return its result before proceeding.
    
    # Check if fixer succeeded
    if [ ! -f "${FIX_REPORT_PATH}" ]; then
      echo "Warning: Iteration ${ITERATION} fixer failed to produce fix report. Stopping auto-loop."
      break
    fi
  done
  
  # After loop completes
  if [ $ITERATION -ge $MAX_ITERATIONS ]; then
    echo ""
    echo "⚠ Reached maximum iterations (${MAX_ITERATIONS}). Remaining issues documented in REVIEW-FIX.md."
  fi
fi
```

Key design decisions for --auto (addresses ALL review HIGH concerns):
1. **Re-review scope**: Uses REVIEW_FILES_ARRAY from original REVIEW.md frontmatter, falling back to full phase scope. Scope is NOT lost between iterations. Uses portable while-read loop (bash 3.2+ compatible, handles spaces in paths).
2. **Artifact semantics**: REVIEW.md is overwritten by each re-review (latest review state). REVIEW-FIX.md is overwritten by each fixer iteration (latest fix state with iteration count). There is ONE final version of each artifact, not per-iteration copies.
   Backup files (.iterN.md) preserve history for post-mortem analysis if iterations degrade.
3. **Commit timing**: Fix commits happen per-finding inside the agent. REVIEW-FIX.md is NOT committed until step 7 (after ALL iterations complete). Only ONE docs commit for REVIEW-FIX.md, not one per iteration.
</step>

<step name="commit_fix_report">
After ALL iterations complete (or single pass in non-auto mode), validate and commit REVIEW-FIX.md:

```bash
set -euo pipefail
if [ -f "${FIX_REPORT_PATH}" ]; then
  # Validate REVIEW-FIX.md has valid YAML frontmatter with status field
  HAS_STATUS=$(REVIEW_PATH="${REVIEW_PATH}" node -e "
    const fs = require('fs');
    const content = fs.readFileSync(process.env.FIX_REPORT_PATH, 'utf-8');
    const match = content.match(/^---\n([\s\S]*?)\n---/);
    if (match && /status:/.test(match[1])) { console.log('valid'); } else { console.log('invalid'); }
  " 2>/dev/null)
  
  if [ "$HAS_STATUS" = "valid" ]; then
    echo "REVIEW-FIX.md created at ${FIX_REPORT_PATH}"
    
    if [ "$COMMIT_DOCS" = "true" ]; then
      maxvision-sdk query commit \
        "docs(${PADDED_PHASE}): add code review fix report" \
        --files "${FIX_REPORT_PATH}"
    fi
  else
    echo "Warning: REVIEW-FIX.md has invalid frontmatter (no status field). Not committing."
    echo "Agent may have produced malformed output. Review manually: ${FIX_REPORT_PATH}"
  fi
else
  echo "Warning: REVIEW-FIX.md not found at ${FIX_REPORT_PATH}."
  echo "Agent may have failed before writing report."
  echo "Check git log for any fix(${PADDED_PHASE}) commits that were applied."
fi
```

This commit happens ONCE at the end of the workflow, after all iterations (if --auto) complete. Not per-iteration.
</step>

<step name="present_results">
Parse REVIEW-FIX.md frontmatter and present formatted summary to user.

First check if fix report exists:

```bash
set -euo pipefail
if [ ! -f "${FIX_REPORT_PATH}" ]; then
  echo ""
  echo "═══════════════════════════════════════════════════════════════"
  echo ""
  echo "  ⚠ No fix report generated"
  echo ""
  echo "───────────────────────────────────────────────────────────────"
  echo ""
  echo "The fixer agent may have failed before completing."
  echo "Check git log for any fix(${PADDED_PHASE}) commits."
  echo ""
  echo "Retry: /maxvision-code-review ${PHASE_ARG} --fix"
  echo ""
  echo "═══════════════════════════════════════════════════════════════"
  exit 1
fi
```

Extract frontmatter fields:

```bash
# Extract only the YAML frontmatter block (between first two --- lines)
set -euo pipefail
FIX_FRONTMATTER=$(REVIEW_PATH="${REVIEW_PATH}" node -e "
  const fs = require('fs');
  const content = fs.readFileSync(process.env.FIX_REPORT_PATH, 'utf-8');
  const match = content.match(/^---\n([\s\S]*?)\n---/);
  if (match) process.stdout.write(match[1]);
" 2>/dev/null)

# Parse fields from frontmatter only (not full file)
FIX_STATUS=$(echo "$FIX_FRONTMATTER" | grep "^status:" | cut -d: -f2 | xargs)
FINDINGS_IN_SCOPE=$(echo "$FIX_FRONTMATTER" | grep "^findings_in_scope:" | cut -d: -f2 | xargs)
FIXED_COUNT=$(echo "$FIX_FRONTMATTER" | grep "^fixed:" | cut -d: -f2 | xargs)
SKIPPED_COUNT=$(echo "$FIX_FRONTMATTER" | grep "^skipped:" | cut -d: -f2 | xargs)
ITERATION_COUNT=$(echo "$FIX_FRONTMATTER" | grep "^iteration:" | cut -d: -f2 | xargs)
```

Display formatted inline summary:

```bash
set -euo pipefail
echo ""
echo "═══════════════════════════════════════════════════════════════"
echo ""
echo "  Code Review Fix Complete: Phase ${PHASE_NUMBER} (${PHASE_NAME})"
echo ""
echo "───────────────────────────────────────────────────────────────"
echo ""
echo "  Fix Scope:       ${FIX_SCOPE}"
echo "  Findings:        ${FINDINGS_IN_SCOPE}"
echo "  Fixed:           ${FIXED_COUNT}"
echo "  Skipped:         ${SKIPPED_COUNT}"
if [ "$AUTO_MODE" = "true" ]; then
  echo "  Iterations:      ${ITERATION_COUNT}"
fi
echo "  Status:          ${FIX_STATUS}"
echo ""
echo "───────────────────────────────────────────────────────────────"
echo ""
```

If status is "all_fixed":
```bash
set -euo pipefail
if [ "$FIX_STATUS" = "all_fixed" ]; then
  echo "✓ All issues resolved."
  echo ""
  echo "Full report: ${FIX_REPORT_PATH}"
  echo ""
  echo "Next step:"
  echo "  /maxvision-verify-work  — Verify phase completion"
  echo ""
fi
```

If status is "partial" or "none_fixed":
```bash
set -euo pipefail
if [ "$FIX_STATUS" = "partial" ] || [ "$FIX_STATUS" = "none_fixed" ]; then
  echo "⚠ Some issues could not be fixed automatically."
  echo ""
  echo "Full report: ${FIX_REPORT_PATH}"
  echo ""
  echo "Next steps:"
  echo "  cat ${FIX_REPORT_PATH}                     — View fix report"
  echo "  /maxvision-code-review ${PHASE_NUMBER}           — Re-review code"
  echo "  /maxvision-verify-work                           — Verify phase completion"
  echo ""
fi
```

```bash
set -euo pipefail
echo "═══════════════════════════════════════════════════════════════"
```
</step>

</process>

<platform_notes>
**Windows:** This workflow uses bash features (arrays, variable expansion, while loops). On Windows, it requires Git Bash or WSL. Native PowerShell is not supported. The CI matrix (Ubuntu/macOS/Windows) runs under Git Bash on Windows runners, which provides bash compatibility.
</platform_notes>

<success_criteria>
- [ ] Phase validated before config gate check
- [ ] Config gate checked (workflow.code_review)
- [ ] REVIEW.md existence verified (error if missing)
- [ ] REVIEW.md status checked (skip if clean/skipped)
- [ ] Agent spawned with correct config (review_path, fix_scope, fix_report_path)
- [ ] Agent failure handled with partial-success awareness (some fix commits may exist)
- [ ] --auto iteration loop respects 3-iteration cap
- [ ] --auto re-review uses persisted file scope (not lost between iterations)
- [ ] REVIEW-FIX.md committed ONCE after all iterations (not per-iteration)
- [ ] Missing fix report handled with explicit error message in present_results
- [ ] Results presented inline with next step suggestion
</success_criteria>

---
> Source: [produtoramaxvision/claude-code-maxvision-orchestration](https://github.com/produtoramaxvision/claude-code-maxvision-orchestration) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-22 -->
