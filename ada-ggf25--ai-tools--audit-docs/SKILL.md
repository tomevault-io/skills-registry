---
name: audit-docs
description: Audit hand-authored project documentation (.md, .txt, .rst, .adoc) for accuracy against the codebase as ground truth. Extracts verifiable claims from each doc, checks them against actual code and manifests, reads in-repo PDFs as reference context, and reports findings ranked by doc importance and staleness severity with targeted edit proposals requiring approval. Global and project-agnostic. Trigger when the user says "audit docs", "audit-docs", "check if the docs are up to date", "are the docs stale", "docs health check", "check documentation accuracy", "is the README up to date", "check README", "documentation drift", "docs out of date", "review project documentation", or "update project docs". SKIP for agent instruction files such as AGENTS.md and CLAUDE.md; use audit-agent-docs for Codex instruction docs. SKIP for auto-generated API docs. Use when this capability is needed.
metadata:
  author: ada-ggf25
---

# Audit project documentation (accuracy + coverage check)

Project-agnostic, global skill. It finds every hand-authored documentation file in the
current repo, extracts verifiable claims from each one, checks those claims against the
actual codebase, and produces a ranked report with targeted fix proposals — requiring
explicit per-finding approval before making any change.

It is the complement to `audit-agent-docs` (which audits Codex instruction files).
This skill covers user-facing and developer-facing docs: README, setup
guides, API references, architecture docs, tutorials, and similar.

## What this skill audits

### In scope
- `README.md` (root and nested), `docs/`, `CONTRIBUTING.md`, `ARCHITECTURE.md`,
  `SECURITY.md`, and any hand-authored `.md`, `.txt`, `.rst`, `.adoc` files.
- In-repo PDFs (design docs, specs): read as reference context to detect discrepancies,
  not as audit targets themselves.

### Always skip
- `AGENTS.md`, `AGENTS.override.md`, `CLAUDE.md`, `.codex/`, `.agents/`, and `.claude/`
  directories when they are agent/tool configuration rather than user-facing docs.
- `CHANGELOG*`, `HISTORY*` — updated at release time, stale by design.
- `LICENSE*`, `NOTICE*` — static legal text.
- Generated output dirs: `node_modules/`, `dist/`, `build/`, `out/`, `vendor/`,
  `.venv/`, `target/`, `__pycache__/`, `.next/`, `coverage/`, Sphinx `_build/`,
  JSDoc/TypeDoc `out/`, Swagger/OpenAPI output files.
- Any file listed in `.gitignore`.

## The claim extraction model

Git churn alone is a weak signal for documentation. The primary mechanism is extracting
**verifiable claims** from each file and checking them against codebase reality.

| Claim type | What to extract | How to verify |
|---|---|---|
| **Code blocks** | Fenced ` ``` ` blocks | Do the referenced functions/classes/files exist? Does the snippet still match the actual source? |
| **Shell commands** | `$` lines, `bash`/`sh` blocks | Does the command, flag, or subcommand still exist in the CLI/Makefile/scripts? |
| **File paths** | Paths in prose or blocks | Does the path still exist in the repo? |
| **Version/dep refs** | "requires Node 16", "Python ≥ 3.8", semver pins | Compare with `package.json`, `pyproject.toml`, `go.mod`, `.tool-versions`, etc. |
| **Symbol refs** | Function, class, config-key names in prose | Do these symbols still exist in the source (`grep`/`Glob`)? |
| **Internal links** | `[text](../other-file.md)` | Does the target path exist? |
| **Env/config keys** | `ENV_VAR`, config field names | Do these appear in the codebase or `.env.example`? |

Claim extraction is heuristic and intentionally non-exhaustive — surface the highest-
confidence mismatches, not every possible discrepancy.

## Importance ranking

Report findings by doc importance first, staleness severity second:

| Tier | Files |
|---|---|
| **Critical** | Root `README.md`, root `docs/getting-started*`, `docs/install*`, `docs/setup*` |
| **High** | `CONTRIBUTING.md`, `docs/quickstart*`, `docs/tutorial*`, onboarding guides |
| **Medium** | `docs/api*`, `docs/reference*`, `ARCHITECTURE.md`, `SECURITY.md` |
| **Low** | Internal/contributor docs, docs already covered by a critical-tier file |

## PDF handling

PDFs in the repo (design docs, specs, RFCs) are read as **reference context**, not
audit targets. If a PDF spec contradicts what the docs or code say, flag the
discrepancy — but do not attempt to edit the PDF.

## Fix granularity

Doc fixes are targeted:

| Finding type | Proposed action |
|---|---|
| Wrong version number / renamed flag | Targeted `Edit` of the specific line — propose and require approval |
| Outdated code block | Propose replacing just that block — require approval |
| Broken file path / symbol ref | Propose the corrected path/name — require approval |
| Entire section describes removed feature | Flag for manual rewrite; do not auto-edit |
| New major feature has no docs | Flag as a coverage gap; offer to draft a stub |

Never propose a full-file rewrite unless the user explicitly asks.

## Procedure

### 1. Orient
- Confirm the cwd is the repo root the user intends (ask if in a monorepo/submodule).
- Find all doc candidates:
  `find . \( -name "*.md" -o -name "*.txt" -o -name "*.rst" -o -name "*.adoc" \) \
   -not -path '*/node_modules/*' -not -path '*/.git/*' -not -path '*/dist/*' \
   -not -path '*/build/*' -not -path '*/vendor/*' -not -path '*/_build/*'`
- Apply the skip list (agent instruction files, CHANGELOG*, LICENSE*, generated dirs).
- Find in-repo PDFs: `find . -name "*.pdf" -not -path '*/node_modules/*'` — note them
  for step 5.
- For a large or unfamiliar repo, delegate the file sweep to a read-only subagent and
  work from its summary.

### 2. Triage by importance + git churn
- Assign each file an importance tier (Critical / High / Medium / Low).
- Get its last-touch timestamp: `git log -1 --format="%aI %s" -- <path>`
- Get a rough churn count since then against the root or nearest source dir.
- Use the churn count to prioritise claim-extraction order (highest churn first within
  each tier); do not skip low-churn files — they may still have broken claims.

### 3. Extract and verify claims (highest-importance files first)
For each doc file (Critical and High tiers always; Medium and Low if feasible):
- Parse the file for claim types listed in the claim extraction table.
- For each claim, verify it against the codebase using `grep`/`Glob`/`Read` on the
  relevant source files or manifests.
- Record: claim text, expected value, actual value, verdict (✓ accurate / ✗ wrong /
  ? unclear / ∅ missing).

### 4. PDF context (if PDFs exist)
- Read in-repo PDFs that appear to be specs or design docs (skip clearly auto-generated
  or legal PDFs).
- Note any discrepancies between PDF content and the current docs or code. Flag these
  as findings but do not propose edits to the PDF.

### 5. Coverage gap scan
- Identify source modules, major features, or CLI subcommands that exist in the code
  but have no corresponding documentation in any audited file.
- Flag clear gaps only; do not pad the list with trivial or internal-only code.

### 6. Cross-file consistency check
- If the same claim (version number, command, path) appears in multiple files, verify
  consistency — a mismatch means at least one is wrong even if the individual check
  was inconclusive.

### 7. Report
Present findings in this order, grouped by doc importance tier:

**For each finding:**
- File path and line reference
- Claim type and the specific text that is wrong/missing
- What the codebase actually shows
- Severity: `✗ Wrong` / `∅ Missing` / `? Unclear`
- Proposed fix (or "flag for manual rewrite" / "flag as gap")

**Then a summary section:**
- Coverage gaps (features/modules with no docs)
- Files with no findings (list briefly — "likely accurate as of `<date>`")

### 8. Apply fixes (per finding, with approval)
For each finding with a proposed targeted edit:
- Show the exact before/after diff.
- Get explicit approval before editing.
- Apply only the approved finding; re-check the surrounding context after editing.
- Never batch-apply all findings without per-finding confirmation.

## Guardrails
- Never modify any documentation file without explicit per-finding user approval.
- Skip agent instruction files entirely; those are `audit-agent-docs`'s domain.
- Skip generated docs; auditing them is misleading (the source is the code annotations).
- Claim extraction is heuristic — flag high-confidence mismatches only; do not report
  every uncertain similarity as a finding.
- Fix granularity must match the finding: line-level fixes for line-level claims, not
  full-file rewrites.
- PDFs are reference context only — never propose edits to them.
- Coverage gaps: flag clear omissions; do not pad the list with trivial internal code.
- For large repos, delegate the initial file sweep to a read-only subagent to avoid
  context overload; never read every file in the main thread.
- Never fabricate codebase facts — base every finding on files you actually read.

---
> Source: [ada-ggf25/AI-Tools](https://github.com/ada-ggf25/AI-Tools) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
