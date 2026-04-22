---
name: smart-test-runner
description: Runs IntegrationTesterApp tests with context-aware tag filtering. Detects modified files via git status, maps plugin/kernel paths to test tags, invokes run-tests.ps1 accordingly, and highlights failures. Use when the user requests to run tests, invokes the smart test runner, or when verifying changes under src/Plugins or src/AnimalKernel per CLAUDE.md workflow.
metadata:
  author: dezverev
---

# Smart Test Runner

## Purpose

Run filtered IntegrationTesterApp tests based on current work context: auto-detect tags from modified files, or accept overrides. Reduces test time during development and enforces CLAUDE.md's "use -t when working on specific features" rule.

## When to Apply

- User asks to run tests, run the tests, or use the smart test runner
- After modifying files under `src/Plugins` or `src/AnimalKernel` and verification is needed
- When user explicitly requests filtered tests (e.g. "run stock tests", "test weather,geolocation")

## Workflow

### 1. Resolve Scope

- **"Run all tests"** or **full suite** → Run full suite (no tag filter). Use `.\run-tests.ps1` with no `-t`.
- **"Run tests verbose"** or **verbose** → Add `--verbose` to the run; still infer or use tags unless full suite is also requested.
- **Explicit tags** (e.g. "run stock tests", "test weather,geolocation") → Use those tags only; do not infer from git.
- **"Run tests"** with no further scope → Infer tags from git status (see step 2).

### 2. Infer Tags from Git (when no explicit tags and not --all)

1. Run `git status --short` (or equivalent) in the repo root to list modified/added files.
2. Normalize paths to forward slashes and match against these rules. Collect **all** matching tags (union).
3. If **any** path under `src/AnimalKernel/` matches → treat as "run all tests" (skip tag filter).
4. Otherwise, apply path → tag mapping:

| Path pattern (under `src/`) | Tags to add |
|----------------------------|-------------|
| `Plugins/**/Stock*`        | `stock`     |
| `Plugins/**/Weather*`     | `weather`   |
| `Plugins/**/RSS*`         | `rss`, `news` |
| `Plugins/**/Outlook*`     | `email`, `outlook` |
| `Plugins/**/Fetch*`       | `fetch`, `web-research` |
| `Plugins/**/Geolocation*` | `geolocation`, `coordinates` |
| `Plugins/**/Philips*`, `**/Hue*` | `philips-hue`, `lights` |
| `Plugins/**/TFT*`         | `tft`       |
| `Plugins/**/*Filesystem*` | `filesystem`, `read` |
| `AnimalKernel/**`         | (run all; no tags) |

5. Deduplicate and format as a single comma-separated string for `-t` (e.g. `rss,news`, `fetch,web-research`).

### 3. Execute Tests

- **Run all**: `.\run-tests.ps1` and, if verbose was requested, `.\run-tests.ps1 --verbose`.
- **Run filtered**: `.\run-tests.ps1 -t <tag1,tag2,...>` (PowerShell: `-t "stock"`, `-t "weather,geolocation"`). Add `--verbose` when user asked for verbose or when summarizing failures.
- Execute from the repository root (`c:\sourceCursor\AnimalAL-v1` or project root). Use `.\run-tests.ps1` as in CLAUDE.md.

### 4. On Failure or When Verbose Requested

- If the run fails or user asked for verbose: re-run with `--verbose` (and same tag set, or no tags for full run) to capture detailed logs.
- Locate the latest test-results JSON under `src/IntegrationTesterApp/test-results/` (or the output folder reported by the script). Parse it and:
  - List failed test names and, if present, failure messages.
  - Highlight which tags were used and that failed tests may need code fixes before re-running.

### 5. After Run: Logical Response Check (per project rules)

- **After each test run** (and especially when all tests pass), run the **check-test-logical-resp** skill on the latest test-results JSON in `src/IntegrationTesterApp/test-results/`. Read `.cursor/skills/check-test-logical-resp/SKILL.md` and follow its workflow to audit passed tests for logical coherence of responses with prompts; report any flagged issues and recommend fixes.

### 6. Report Back

- State whether scope was inferred or overridden.
- State tags used (or "full suite").
- If failures occurred: list failed cases and recommend fixing the code (per CLAUDE.md), then re-running tests.
- Include the outcome of the logical-response check (e.g. "Logical check: N passed tests reviewed, M issues" or "All passed tests logically coherent").

## Usage Examples

- **Auto-detect**: User says "run tests" → infer tags from git, then `.\run-tests.ps1 -t <tags>`.
- **Specific tags**: "run stock tests" or "test weather,geolocation" → `.\run-tests.ps1 -t stock` or `-t "weather,geolocation"`.
- **Full suite**: "run all tests" → `.\run-tests.ps1` (no `-t`).
- **Verbose**: "run tests verbose" or "run stock tests with verbose" → add `--verbose` to the same invocation.

## Implementation Notes

- Use `git status` (or `git diff --name-only`) to get modified/added paths; normalizing to repo-relative paths avoids false negatives.
- Pattern matching: "path under `src/`" means path contains `Plugins/...` or `AnimalKernel/...`; glob-style `**` means "any subpath." Match case-insensitively if the OS or tool outputs mixed case.
- run-tests.ps1 accepts `-t <tags>` and `--verbose`; see repo `run-tests.ps1` and CLAUDE.md for exact flags. Full suite = omit `-t`.
- run-tests.ps1 may stop any existing IntegrationTesterApp processes before building to avoid locked DLLs (use `--no-kill` to skip this).

## Relation to CLAUDE.md

- Prefer filtered runs (`-t`) unless kernel code or "run all" was explicitly requested.
- On failure, fix the code first; do not relax test expectations.
- After each test run, run **check-test-logical-resp** on the latest test-results JSON (required by project rules). Then analyze outputs: if answers or plugin usage look wrong, make code or test changes as needed.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/dezverev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
