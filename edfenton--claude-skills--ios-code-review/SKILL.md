---
name: ios-code-review
description: Review iOS code for compliance with standards, NFRs, and security policy. Use when this capability is needed.
metadata:
  author: edfenton
---

## Purpose

Review code against ios-std, ios-nfr, and ios-sec policies. Report issues, then (with approval) fix and run tests to confirm.

## Arguments

- `--paths <glob>` — Limit review scope (default: whole repo)
- `--no-fix` — Report only, don't offer to fix

## Workflow

### 1. Automated gates

```bash
./scripts/lint.sh      # SwiftLint
./scripts/format.sh    # SwiftFormat (check mode)
```

If scripts don't exist, note that repo doesn't match scaffold expectations.

### 2. Policy review

Review against:

- **ios-std** — MVVM boundaries, SwiftUI conventions, project structure
- **ios-nfr** — performance, accessibility, offline correctness, app store readiness
- **ios-sec** — keychain usage, ATS, deep link validation, logging hygiene

For each issue, note:

- Category: `std` | `nfr` | `sec`
- Severity: `must-fix` | `should-fix` | `nice-to-have`
- File and location
- What's wrong and how to fix it

### 3. Report results

Summary of automated gate results + policy findings grouped by severity.

### 4–5. Approval gate, fix and confirm

See `/shared-review-workflow` for severity definitions, approval gate protocol, and fix constraints. Run `/ios-unit-test` to confirm no regressions after fixes.

## Reference

For review checklists and common issues, see `reference/ios-code-review-reference.md`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/edfenton) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
