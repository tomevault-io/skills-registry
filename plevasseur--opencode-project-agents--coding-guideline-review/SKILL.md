---
name: coding-guideline-review
description: Review a coding guideline PR for merge readiness. Use when this capability is needed.
metadata:
  author: plevasseur
---

# Coding Guideline Review

## Purpose

Provide a consistent, evidence-based review process for coding guideline PRs, aligned with repository style rules, validation checks, and authoritative Rust documentation. Reviews must hold a high bar for correctness because these guidelines are used in safety-critical software.

## Inputs

- Required: `PR_URL: <url>`

## Steps

1. Fetch PR context and changes.
   - Use `gh pr view` to capture title, body, labels, reviewers, and status checks.
   - Use `gh pr diff` to identify the guideline files and any non-guideline changes.
   - Confirm the PR body includes `closes #<id>` when applicable.
2. Verify CI and status checks.
   - Ensure `build` and `check_typos` are successful.
   - Note Netlify and header/redirect checks (informational unless policy says otherwise).
   - If anything fails, record the failure reason and classify it (spec lock diff, rust-example issues, inline URLs, bibliography problems, invalid FLS IDs).
3. Validate placement and file structure.
   - Guidelines belong under `src/coding-guidelines/<chapter>/gui_<id>.rst.inc` with the chapter index updated.
   - The chapter should align with the FLS section for the guideline.
   - If a guideline replaces an existing one, the old guideline should be retained as `status: retired` or a migration rationale must be provided.
4. Check guideline structure and required fields.
   - Required fields are present: `category`, `status`, `release`, `fls`, `decidability`, `scope`, `tags`.
   - Field values are valid:
     - `category`: mandatory, required, advisory, disapplied
     - `status`: draft, approved, retired
     - `decidability`: decidable, undecidable
     - `scope`: module, crate, system
   - Validate field choices against `src/process/style-guideline.rst` and `.github/ISSUE_TEMPLATE/CODING-GUIDELINE.yml`.
   - Every field choice must have a justification or citation in `claims.md`.
   - Title and amplification are normative and use RFC 2119 language (MUST, SHOULD, MAY) when appropriate.
   - Exceptions are explicit and only used when needed.
5. Verify FLS linkage and scope alignment.
   - `:fls:` uses a valid FLS paragraph ID and matches the guideline scope.
   - The guideline content maps to the referenced FLS paragraph without going beyond it.
   - If multiple FLS paragraphs are needed, split into multiple guidelines.
6. Enforce citations and bibliography rules.
   - No inline URLs in guideline text.
   - Use `:std:` for standard library, core, and alloc references.
   - Use `:cite:` and `:bibentry:` for external sources (Rust Reference, CERT, MISRA, etc.).
   - Citation keys are UPPERCASE-WITH-HYPHENS, 50 characters max, and consistent for identical URLs across guidelines.
7. Validate technical correctness against authoritative sources.
   - Validate all claims against the Rust Reference, Unsafe Code Guidelines, and std/core/alloc docs.
   - Use additional authoritative sources as needed (Rustonomicon, RFCs, edition guide, rustc dev guide, compiler behavior notes).
   - Claims include guideline text, rationale, example prose, and inline code comments.
   - If a claim cannot be verified or appears incorrect, treat it as a blocking issue.
8. Map claims to sources in `claims.md`.
   - Record every claim with its location, text, type, and supporting source.
   - Use normative sources when available; otherwise use authoritative non-normative sources (FLS, UCG, rustc dev guide) or referenced standards (CERT, MISRA, etc.).
   - Include every field choice (category, status, release, fls, decidability, scope, tags) as a claim with justification.
   - Any unverified claim is a blocking issue.
9. Review rust-example usage and correctness.
   - Each guideline includes at least one non-compliant and one compliant example.
   - Examples use `.. rust-example::` (not `code-block`).
   - Non-compliant examples only violate the current guideline; compliant examples adhere to all guidelines.
   - Directive options match expected behavior:
     - `:compile_fail:` for compile errors
     - `:should_panic:` for runtime panics
     - `:no_run:` for compile-only examples
   - Avoid warnings or add `:warn: allow` with rationale.
   - Unsafe code must include `:miri:` (use `expect_ub` or `skip` with justification as needed).
10. Check cross-guideline consistency.
   - If overlapping with existing rules, link to them and describe the relationship (stronger, weaker, subset).
   - If the guideline is intended as a replacement, ensure cross-references are updated accordingly.
11. Validate external standard coverage claims.
   - If the PR claims to cover CERT, MISRA, or similar standards, verify the guideline text actually covers those requirements.
   - Include citations and note any limitations or partial coverage explicitly.
12. Determine verdict (approve, comment, or request changes).
   - Approve only if CI is green and correctness is verified with no blocking issues.
   - Request changes if any blocking issue exists or correctness cannot be verified.
   - Comment only when issues are non-blocking or CI failures are clearly infra-related.
13. Deliver review artifacts under `$OPENCODE_CONFIG_DIR/reviews`.
   - Create `pr-<number>-<short-slug>/summary.md` for the final verdict and rationale.
   - Create `comment-001.md`, `comment-002.md`, etc., for each review comment.
   - If there are zero issues, only create `summary.md`.
   - Each comment file uses this template and includes GitHub suggestion blocks for edits:

      ````md
      ### Location
      File: <path>
      Line: <line-number>
      Snippet: `<leading text from the line>`

      ### Type
      Blocking | Non-blocking | Question

      ### Comment
      <comment text, with references>

      ```suggestion
      <replacement or insertion text>
      ```
      ````
   - Use GitHub suggestion blocks whenever text should be added or replaced.
   - Create `claims.md` with a claims-to-sources map and summary table:

      ```md
      # Claims Map

      PR: <number>
      Guideline IDs: <gui_...>
      Reviewer: <name>
      Date: <yyyy-mm-dd>

      ## Summary Table
      | ID | Status | Type | Location | Source Kind | Notes |
      | --- | --- | --- | --- | --- | --- |
      | C-001 | Verified | Normative | <path>:<line> | Reference | <short note> |

      ## Legend
      - Type: Normative | Rationale | Example-prose | Example-comment | Field-choice
      - Source kind: Reference | FLS | UCG | Rustc-dev-guide | Standard (CERT/MISRA/...) | Other
      - Status: Verified | Unverified | Disputed

      ## Claims

      ### C-001
      Location:
      - File: <path>
      - Line: <line>
      - Snippet: `<leading text>`

      Claim:
      - Text: "<verbatim claim>"
      - Type: Normative

      Source:
      - Kind: Reference
      - Citation: <link or :cite: key>
      - Notes: <short explanation mapping claim to source>

      Status:
      - Verified
      ```

## Output

- `summary.md` with verdict, CI status, blocking issues, and correctness evidence.
- One `comment-###.md` per review comment using the required template and suggestion blocks.
- `claims.md` with a claims-to-sources map and summary table.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/plevasseur) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
