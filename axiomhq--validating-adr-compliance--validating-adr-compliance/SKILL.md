---
name: validating-adr-compliance
description: Validates PR code against Architecture Decision Records (ADRs). Uses oracle for deep analysis of implementation compliance. Use when asked to validate a PR against an ADR, check ADR compliance, or review implementation adherence to architectural decisions.
metadata:
  author: axiomhq
---

# Validating ADR Compliance

Validates that PR code and related implementations adhere to Architecture Decision Records.

## When to Use

- Validating a PR implements an ADR correctly
- Checking if code changes align with documented architectural decisions
- Reviewing implementation details against ADR specifications
- Identifying deviations from accepted architecture

## Preconditions & Assumptions

Before starting, confirm these assumptions hold:

- **ADR location**: ADRs are stored under `doc/adr/` by default. Adjust if the repo uses a different path.
- **Base branch**: The default comparison branch is `main` (or `origin/main`). Adjust for repos using `master` or another base.
- **PR context**: This skill is optimized for validating a specific PR/branch vs its base.

If assumptions don't hold, the user must provide:
- The ADR ID or file path
- The set of implementation files or modules to review

## Workflow

### Step 1: Identify the ADR

**Default behavior**: Automatically detect ADRs added/modified in the current PR.

Run this command to find ADRs changed in the current branch:
```bash
git diff --name-only origin/main...HEAD -- doc/adr/
```

If no ADRs found in the diff, try with `main`:
```bash
git diff --name-only main...HEAD -- doc/adr/
```

Use the repository's default branch as the base for diffs (commonly `main` or `master`). Adjust the git commands accordingly.

#### Handling Multiple ADRs

If multiple ADRs are added/modified in the PR, treat each ADR separately:

- Build a separate requirements checklist per ADR
- Map implementation files to each ADR where possible
- Produce one compliance report per ADR

#### Check ADR Status

When reading the ADR, note its **Status**:

- **Accepted**: Enforce compliance—this is the primary case
- **Superseded**: Identify the superseding ADR and validate against that instead
- **Proposed/Draft**: Treat findings as advisory—decision is not final
- **Deprecated**: Do not enforce; explain this ADR should not be validated

If the ADR has a `Supersedes` or `Superseded by` field, always prefer the latest ADR in the chain for compliance checks.

#### Fallback Options

If no ADR in PR diff:

1. User provides the ADR number (e.g., "ADR-0052")
2. User references a specific ADR file
3. Search `doc/adr/` for relevant ADRs based on PR topic keywords

If multiple candidate ADRs appear relevant based on keywords or status, present the top candidates and ask the user to confirm which ADR(s) to validate against.

#### No ADR Found

If no relevant ADR is found:

- Report explicitly: "No applicable ADR identified for this PR based on doc/adr/ and provided inputs."
- Ask the user to provide a specific ADR ID or confirm that no ADR validation is required
- Do NOT fabricate compliance concerns

Once identified, read the full ADR:
```
Read doc/adr/XXXX-<name>.md
```

### Step 2: Extract ADR Requirements

Parse the ADR and extract a structured requirements table:

| ID | Type | Description | Source (ADR quote/section) | Verifiability |
|----|------|-------------|---------------------------|---------------|
| ADR-XXXX-R1 | API | Endpoint must exist at /path | "Decision – We add..." | Strict |
| ADR-XXXX-R2 | Security | Header X-Org-Id required | "API: All requests MUST..." | Strict |
| ADR-XXXX-R3 | Performance | Call must be rate limited | "Consequences – expensive..." | Heuristic |

**Requirement types**: Functional / API / Data / Performance / Security / Observability / Operational / Other

**Verifiability levels**:
- **Strict**: Exactly testable in code
- **Heuristic**: Approximate, requires judgment
- **Non-verifiable**: Documentation/assumption only—cannot assert from code

For vague high-level statements (e.g., "should scale well"), still record them but mark as `Heuristic` or `Non-verifiable`. Treat these separately from strict requirements in the report.

### Step 3: Identify Implementation Files

Find all code related to the ADR implementation, starting narrow and expanding as needed:

#### Start from PR Diff

1. List all files changed in the PR
2. Filter to files likely related to the ADR by:
   - Matching ADR title keywords, endpoint paths, or key terms
   - File location (service/module that owns the feature)

#### Expand if Necessary

Only if needed, broaden search beyond PR files:

- Use `finder` to locate implementations based on ADR keywords
- Use `Grep` for specific API endpoints, struct names, or patterns
- Include supporting modules that PR files depend on if behavior is non-local

#### Check All Artifact Types

Check for all implementation artifacts, not just application code:

- API/router handlers
- Domain/service logic
- Configuration and feature flags
- Infrastructure-as-code or deployment configs
- Documentation and API specs (if ADR requires them)
- Test files (unit, integration, e2e)

#### Monorepo Hint

In monorepos, narrow search to the service or package that owns the ADR's domain before broadening to the whole repo.

### Step 4: Deep Analysis with Oracle

**CRITICAL**: Use the oracle tool for the compliance analysis. This is a complex research task requiring expert reasoning.

Call the oracle with:
- **task**: "Validate implementation compliance against ADR"
- **context**: Include:
  - The full ADR text
  - The structured requirements table from Step 2
  - List of implementation files found
- **files**: All relevant implementation files (as JSON array)

#### Oracle Prompt Template

```
Task: Validate that the implementation in these files correctly adheres to ADR-XXXX (<title>)

Context:
ADR-XXXX specifies:
[Paste structured requirements table here]

Please:
1. For each requirement in the table, determine:
   - Status: PASS / FAIL / UNKNOWN
   - Evidence: file path(s) and line number(s)
   - Reasoning: brief justification (1–3 sentences)

2. Highlight any potential conflicts with other ADRs (if visible).

3. Identify any tests that validate or should validate these requirements.

4. Explicitly call out any requirements that cannot be verified with the provided files (mark as UNKNOWN).

Files: ["path/to/handler.rs", "path/to/types.rs", "path/to/tests.rs"]
```

#### Handling Large Files

If implementation files are large:

- Prefer including only the relevant sections (handlers, functions, types) instead of entire files
- If you must split files, clearly label chunks (e.g., `handler.rs [lines 1–200]`)
- Ensure each code snippet is accompanied by its file path and line range for traceability

### Step 5: Report Findings

Structure the compliance report:

```markdown
## ADR Compliance Report: ADR-XXXX

### Summary
[COMPLIANT / PARTIAL / NON-COMPLIANT]

### Requirements Checklist
- [ ] ADR-XXXX-R1 – [PASS/FAIL/UNKNOWN] – Evidence: [file:line or "Not found"]
- [ ] ADR-XXXX-R2 – [PASS/FAIL/UNKNOWN] – Evidence: [file:line]
...

### Deviations Found
1. **[Deviation]**: Description
   - Expected: (from ADR)
   - Actual: (in code)
   - File: [link to file:line]
   - Severity: [Critical/Major/Minor]

### Assumptions & Limitations
- List any assumptions made (e.g., "Assume auth middleware enforces header globally")
- Note any missing context or code that prevented full verification

### Recommendations
- Actionable fixes for any deviations

### Files Reviewed
- [List of all files analyzed with links]
```

#### Result States

- **PASS**: Requirement is demonstrably satisfied with evidence
- **FAIL**: Requirement is violated or missing
- **UNKNOWN**: Requirement cannot be verified from provided code (config not present, depends on external systems, ADR too vague)

#### Severity Definitions

- **Critical**: Violates core decision or breaks contract (API shape, required fields, major constraints)
- **Major**: Violates important but non-fatal aspects (performance safeguards, logging, observability)
- **Minor**: Style, minor deviations, or missing documentation that do not change core behavior

## Edge Cases & Failure Modes

### Partial Implementation PRs

If the PR intentionally implements only part of an ADR:

- Note this in the summary: "Partial implementation of ADR-XXXX per PR scope."
- Mark unimplemented requirements as `NOT-IN-SCOPE` (if user confirms) or `UNKNOWN`

### Contradictory ADRs

If a requirement appears to conflict with another ADR:

- Call this out in "Deviations Found" as an **Architectural Conflict**
- Recommend reconciling the ADRs rather than the implementation alone

### Superseded or Deprecated ADRs

- **Superseded**: Validate against the superseding ADR instead
- **Deprecated**: Do not enforce; treat analysis as historical/contextual only

## ADR Location

ADRs are stored in: `doc/adr/`

Format: `XXXX-<short-name>.md` (e.g., `0052-metrics-lookup-for-tag-values.md`)

## ADR Structure Reference

Each ADR typically contains:

- **Title**: `# N. <decision title>`
- **Date**: When the decision was made
- **Status**: Proposed, Accepted, Deprecated, Superseded
- **Supersedes/Superseded by**: References to related ADRs (if any)
- **Context**: Problem and constraints
- **Decision**: The architectural choice
- **Consequences**: Trade-offs and outcomes

## Tips for Effective Validation

1. **Be Literal**: Check exact API paths, field names, types
2. **Check Edge Cases**: ADRs often mention error handling or limitations
3. **Verify Consequences**: If ADR says "expensive call", look for rate limiting/guards
4. **Test Coverage**: Verify tests exist for ADR requirements
5. **Look for Superseded ADRs**: An ADR may update a previous decision
6. **Scope to PR First**: Start narrow from the diff, expand only as needed
7. **Use UNKNOWN Honestly**: Don't claim PASS without evidence; don't claim FAIL without proof

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/axiomhq) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
